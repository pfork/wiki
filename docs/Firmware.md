# PITCHFORK Firmware

This page attempts to document everything related to the firmware of the PITCHFORK.

## Hardware Requirements

- a PITCHFORK (or two if you want to play with the embedded radio transmitter)
- a JTAG debugger ([Blackmagic](https://github.com/blacksphere/blackmagic/) prefered, you can [flash your own for cheap on a discovery board](https://embdev.net/articles/STM_Discovery_as_Black_Magic_Probe))
- some USB cables
- a host - a PC for now (and yes, MACs are also personal computers).

## Software Requirements

There is a [setup.sh](../git/firmware/tree/setup.sh) in the firmware
repository, which should prepare your firmware dependencies correctly.
What this script does - follows below.

### get toolchain

Either build your own, you can find a cross-compiler [build-script](../git/toolchain/tree/tchain.sh) in our
toolchain gitrepo, or you can download one from [launchpad](https://launchpad.net/gcc-arm-embedded/+download) and unpack it.
Set your PATH to include the `bin` directory of this cross-compiler, e.g.:
`export PATH="$PATH:$(realpath toolchain/gcc-arm-none-eabi-5_4-2016q2/bin/)"`

### clone&build libopencm3

Some of the USB handling depends on [libopencm3](http://libopencm3.org)

```
cd lib/
git clone https://github.com/libopencm3/libopencm3.git
cd libopencm3
git checkout 5ea4e8842a786dfc6314d7ac85ddbef4abbcde35
FP_FLAGS="-mfloat-abi=soft" CFLAGS='-mno-unaligned-access -g -Wall -Werror -Os -mfix-cortex-m3-ldrd -msoft-float -mthumb -Wno-strict-aliasing -fomit-frame-pointer -mthumb -mcpu=cortex-m3 -fstack-protector --param=ssp-buffer-size=4' make
```

### clone&build liblzg

[liblzg](https://github.com/mbitsnbites/liblzg) is used to compress some data used in the firmware, like the logo and pgpwords.

```
git clone https://github.com/mbitsnbites/liblzg.git
cd liblzg/src
make
```
### clone&build libsodium
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

### install pysodium

[pysodium](https://github.com/stef/pysodium) is used as a frontend for libsodium during the build of the firmware, install it globally or in your virtualenv

```
pip install pysodium
```

also install libsodium on your host, the normal OS packages should be ok, e.g:
```
apt-get install libsodium
```

### setting up dbus rules for correct permissions

enable dbus for the pitchfork in `/etc/udev/rules.d/40-pitchfork.rules`
```
SUBSYSTEM=="usb", ATTRS{idVendor}=="0483", ATTRS{idProduct}=="df11", GROUP="plugdev", MODE="0660"
SUBSYSTEM=="usb", ATTRS{idVendor}=="0483", ATTRS{idProduct}=="5740", GROUP="plugdev", MODE="0660"
```
