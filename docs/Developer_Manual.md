# Developer manual

Welcome dear developers & contributors

## Hardware Requirements

- a PITCHFORK (or two)
- a jtag debugger (blackmagic prefered, you can flash your own for cheap on a discovery board)

## Software Requirements

There is a setup.sh in the firmware repository.

## Repositories

Project PITCHFORK is compartmentalized into the following main repositories

- [hardware](/git/hardware/) Hardware kicad files for PITCHFORK
- [firmware](/git/firmware/) PITCHFORK firmware - the crypto has been broken out to facilitate better reviews and re-use into submodules:
    * [newhope-cm3](/git/newhope-cm3/) newhope key-exchange optimized for cortex m3
    * [pitchforkedsphincs](/git/pitchforkedsphincs/) armedsphincs ported to pitchfork
    * [scalarmult](/git/scalarmult/) cortex-m optimized scalarmult implementation from munacl
    * [xeddsa](/git/xeddsa/) port of libsignals xeddsa implementation to the pitchfork
- [software](/git/hardware/) PITCHFORK library for the host side with a simple cli
- [toolchain](/git/toolchain/) firmware development tools: cross-compiler builder, sniffer, jtag debugging with python.

- [wiki](/git/wiki/) repo for the pitchfork wiki - you're reading it now!

- [tests](/git/wiki/) tests - also handy as examples/useful snippets to be re-used
