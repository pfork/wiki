# PITCHFORK Firmware

This page attempts to document everything related to the firmware of the PITCHFORK.

## Hardware Requirements

- a PITCHFORK (or two if you want to play with the embedded radio transmitter)
- a JTAG debugger ([Blackmagic](https://github.com/blacksphere/blackmagic/) prefered, you can [flash your own for cheap on a discovery board](https://embdev.net/articles/STM_Discovery_as_Black_Magic_Probe))
- some USB cables
- a host - a PC for now (and yes, Macs are also personal computers).

## Software Requirements

First of all you need a cross-compiler, and then you need to build the
dependencies and the firmware itself with it. Either you download a
precompiled one, or use the bundled [tchain.sh](#get-toolchain) script mentioned below.

Since some dependencies are included as submodules you should use
`--recursive` when cloning the firmware repository, like so:

```git clone --recursive https://pitchfork.ist/git/firmware.git```

There is a few other dependencies which are not handled as submodules.
A [setup.sh](../git/firmware/tree/setup.sh) script in the firmware
repository should automatically prepare your firmware dependencies correctly.
What this script does - follows below.

### Get toolchain

Either build your own, you can find a cross-compiler [build-script](../git/toolchain/tree/tchain.sh) in our
toolchain gitrepo, or you can download one from [launchpad](https://launchpad.net/gcc-arm-embedded/+download) and unpack it.
Set your PATH to include the `bin` directory of this cross-compiler, e.g.:

```
export PATH="$PATH:$(realpath toolchain/gcc-arm-none-eabi-5_4-2016q2/bin/)"
```

If you compile using tchain.sh, make sure you install the dependencies
for building gcc:
```
apt-get install libgmp-dev libmpfr-dev libmpc-dev texinfo
```

### Clone&build libopencm3

Some of the USB handling depends on [libopencm3](http://libopencm3.org)

```
cd lib/
git clone https://github.com/libopencm3/libopencm3.git
cd libopencm3
git checkout 5ea4e8842a786dfc6314d7ac85ddbef4abbcde35
FP_FLAGS="-mfloat-abi=soft" CFLAGS='-mno-unaligned-access -g -Wall -Werror -Os -mfix-cortex-m3-ldrd -msoft-float -mthumb -Wno-strict-aliasing -fomit-frame-pointer -mthumb -mcpu=cortex-m3 -fstack-protector --param=ssp-buffer-size=4' make
```

Make sure you use the cross-compiler toolchain to build this.

### Clone&build liblzg

[liblzg](https://github.com/mbitsnbites/liblzg) is used to compress some data used in the firmware, like the logo and pgpwords.

```
git clone https://github.com/mbitsnbites/liblzg.git
cd liblzg/src
make
```

Here you should also make sure you use the cross-compiler toolchain to build this.

### Clone&build libsodium
[libsodium](https://libsodium.org) is the main cryptographic engine used in the PITCHFORK, it needs to be patched though :/
The reasons for the patch are:
 - no abort(3) in the firmware
 - no sha* in the firmware, everything is generichash-based.

```
cd lib/
git clone https://github.com/jedisct1/libsodium.git
cd libsodium
patch -p1 <../libsodium-pitchfork.patch
./autogen.sh
CFLAGS='-DNDEBUG -mthumb -mcpu=cortex-m3 -fno-strict-overflow -mno-unaligned-access -fvisibility=hidden -Os -fomit-frame-pointer -mfix-cortex-m3-ldrd -msoft-float -fdata-sections -ffunction-sections -nostdlib -nostartfiles -ffreestanding' ./configure --host=arm-none-eabi && make all
```

And again, you shoulde use the cross-compiler toolchain to build this.

### Install pysodium

[pysodium](https://github.com/stef/pysodium) is used as a frontend for libsodium during the build of the firmware, install it globally or in your virtualenv

```
pip install pysodium
```

### Install pysodium

Also install libsodium on your host, the normal OS packages should be ok, e.g:
```
apt-get install libsodium
```

If you get build errors when linking the signer, complaining about a
few missing symbols starting with `crypto_core_curve25519_ref10_`,
make sure that you have a more recent version than 1.0.8 of
libsodium. On debian related systems the version is misleading and
despite claiming 1.0.11 it seemingly still 1.0.8 or earlier.

### Setting up dbus rules for correct permissions

enable dbus for the pitchfork in `/etc/udev/rules.d/40-pitchfork.rules`
```
SUBSYSTEM=="usb", ATTRS{idVendor}=="0483", ATTRS{idProduct}=="df11", GROUP="plugdev", MODE="0660"
SUBSYSTEM=="usb", ATTRS{idVendor}=="0483", ATTRS{idProduct}=="5740", GROUP="plugdev", MODE="0660"
```

## Setting your personal firmware signature key

Before compiling the firmware, you need to create your personal
*firmware signing key*. This will be used to sign the firmware you
compile. During boot the signature is checked if it is signed either
by the main developer signing key, or your key that you're about to
generate.

The PITCHFORK has a master signing public key which authorizes any
firmware produced by team PITCHFORK, however a more technical-minded
user might want to create their own firmware. To allow the custom
firmware to boot (assuming it verifies the signature) or normal
firmware upgrade process to work the user has to set their own
signature verification key. These verification keys are stored in a
one-time-programmable area, once set, they cannot be changed.

1. Generate your key on your computer in the firmware root using
   `./tools/createsignkey.py signer/master.key`
2. Make a secure backup of the master.key, you will need it whenever
   you want to flash your own signature verified firmware.
3. Store it to your one-time-programmable area by taking the output of
   step1 (the line which starts with `const unsigned char pk`)
4. change tools/store-key.c pk to the line from step 2.
5. change tools/store-key.c block from 0 to 1.
6. compile it
7. run the resulting binary in RAM using a JTAG debugger.
