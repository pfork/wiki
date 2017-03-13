# Developer manual

Welcome dear developers & contributors

## Hardware Requirements

- a PITCHFORK (or two)
- a jtag debugger (blackmagic prefered, you can [flash your own for cheap on a discovery board](https://embdev.net/articles/STM_Discovery_as_Black_Magic_Probe))

## Software Requirements

There is a [setup.sh](../git/firmware/tree/setup.sh) in the firmware repository.

## Repositories

Project PITCHFORK is compartmentalized into the following main repositories

- [hardware](../git/hardware/) Hardware kicad files
- [firmware](../git/firmware/) The firmware - the 3rd-party crypto has been broken out to facilitate better reviews and re-use into submodules:
    * [newhope-cm3](../git/newhope-cm3/) Newhope key-exchange optimized for cortex m3
    * [pitchforkedsphincs](../git/pitchforkedsphincs/) Armedsphincs ported to pitchfork
    * [scalarmult](../git/scalarmult/) Cortex-Mx optimized scalarmult implementation from munacl
    * [xeddsa](../git/xeddsa/) Port of libsignals xeddsa implementation
- [software](../git/hardware/) Library for the host side with a simple cli
- [toolchain](../git/toolchain/) Firmware development tools: cross-compiler build-script, sniffer, jtag debugging with python.

- [wiki](../git/wiki/) the wiki sources - you're reading the compiled version now!

- [tests](../git/wiki/) tests - also handy as examples/useful snippets to be re-used
