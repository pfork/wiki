# Copyright information

The PITCHFORK itself is licensed under the GNU Public License v3.0+

Other components used in this project and their licenses are listed below.

- smallfonts: Software License Agreement (BSD License)
- libopencm3: (L)GPLv3+
- libsodium: ISC
- (armed)sphincs: public domain
- new hope: public domain
- stfs: lgplv2.1+
- xeddsa: gplv3.0

There is a few components which need clarification or replacement with
compatible licenses, see the following sections:

## Low-level functions

 - memcpy: bsd,
 - memset, memcmp, memmove: newlib.

## USB mass storage controller & SDIO driver

Licensed under the terms of: [MCD-ST Liberty SW License Agreement 20Jul2011 v0.1.pdf](https://pitchfork.ist/git/firmware/plain/usb/msc/MCD-ST%20Liberty%20SW%20License%20Agreement%2020Jul2011%20v0.1.pdf)

This is clearly a non-free license, and the implementation should be
replaced by one based on libopencm3. If you want, you can compile
without this feature by removing the HAVE_MSC define from the
makefile.

## Entropy mixer

This code is taken from the Linux kernel, which is gpl2.0. This must
be fixed by re-implementing this module, or finding an equivalently
reviewed and compatibly licensed implementation.

## liblzg

Copyright (c) 2010-2015 Marcus Geelnard

This software is provided 'as-is', without any express or implied
warranty. In no event will the authors be held liable for any damages
arising from the use of this software.

Permission is granted to anyone to use this software for any purpose,
including commercial applications, and to alter it and redistribute it
freely, subject to the following restrictions:

1. The origin of this software must not be misrepresented; you must not
   claim that you wrote the original software. If you use this software
   in a product, an acknowledgment in the product documentation would
   be appreciated but is not required.

2. Altered source versions must be plainly marked as such, and must not
   be misrepresented as being the original software.

3. This notice may not be removed or altered from any source
   distribution.
