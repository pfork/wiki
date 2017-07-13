## Repositories

Project PITCHFORK uses their own git server for version control on
this site here under [/git/](../git/), as a secondary git site we also
use [github](https://github.com/pfork/) on github you can send pull
requests, open issues and review the code. The primary git server will
automatically push new commits via githooks to github.

Project PITCHFORK is compartmentalized into the following main repositories.

- [hardware](../git/hardware/) Hardware kicad files
- [firmware](../git/firmware/) The firmware - the 3rd-party crypto has been broken out to facilitate better reviews and re-use into submodules:
    * [newhope-cm3](../git/newhope-cm3/) Newhope key-exchange optimized for cortex m3
    * [pitchforkedsphincs](../git/pitchforkedsphincs/) Armedsphincs ported to pitchfork
    * [scalarmult](../git/scalarmult/) Cortex-Mx optimized scalarmult implementation from munacl
    * [xeddsa](../git/xeddsa/) Port of libsignals xeddsa implementation
- [software](../git/software/) Library for the host side with a simple cli
- [toolchain](../git/toolchain/) Firmware development tools: cross-compiler build-script, sniffer, jtag debugging with python.

- [wiki](../git/wiki/) the wiki sources - you're reading the compiled version now!

- [tests](../git/tests/) tests - also handy as examples/useful snippets to be re-used

These repos are hosted at pitchfork.ist, but secondary repos also
exist at https://github.com/pfork - here you can also open issues or
review the code.


## Recommended directory layout

```
hardware/         # hardware
toolchain/        # toolchain
src/              # firmware
   lib/
      libsodium/  # libsodium [1]
      liblzg/     # liblzg [1]
      libopencm3/ # libopencm3 [1]
      newhope/    # newhope-cm3 [2]
      scalarmult/ # scalarmult [2]
      sphincs/    # pitchforkedsphincs [2]
      xeddsa/     # xeddsa  [2]
   tests/         # tests
 software/        # software
 wiki/            # wiki
```

[1]: this can be setup manually, or using setup.sh found in the root of the firmware repo

[2]: these are submodules that should be automatically populated
