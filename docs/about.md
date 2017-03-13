```
PITCHFORK (TOP SECRET UMBRA//COMSEC//EYES ONLY//20380119)

o (U//FOUO) PITCHFORK is a device for compartmentalizing key material
  and cryptographic operations in a small and durable USB device.
o (U//FOUO) PITCHFORK is a very simple concept addressing many of the
  weaknesses of the existing operational security vulnerabilities
  revealed by the Snowden leaks. PITCHFORK simple concept breaks with
  traditional and complex or obsolete protocols like x509 or PGP yet does
  not try to reinvent anything and reuses widely reviewed implementations.
o (S//CS//NF) PITCHFORK avoids public key cryptography, creating a
  closed system, where peers have to interact initially for exchanging keys.
o (S//CS//NF) PITCHFORK avoids fingerprintable output to evade being
  used as a "strong selector" by intercepting adversaries.
o (TS//CS//EO) PITCHFORK contains internal flash memory for storing the
  encrypted key-material which can store about 4096 encrypted keys.
o (TSU//CS//EO) PITCHFORK operates as an external cryptographic
  co-processor over a full-speed USB link accessible in user-space
  using python-bindings.
o (TSU//CS//EO) PITCHFORK uses the cryptographic library libsodium (a NaCl
  derivative), New Hope, Armed Sphincs and μNaCl for cryptographic operations.
o (TSU//CS//EO) multiple PITCHFORKs can exchange (ECDH/MPECDH)
  cryptographic key-material using an internal RF transmitter (2.4GHz,
  10-15m range).
o (TSU//CS//EO) PITCHFORKs can pair-wise exchange
  cryptographic key-material using the New Hope post-quantum key exchange
  protocol using the internal RF transmitter.
o (TSU//CS//EO) Multiple entropy sources are harvested:
 - the 32 bit internal analog RNG of the underlying microprocessor and
 - an intentionally miscalibrated ADC reading the VREF, an internal
  temperature sensor and VBAT voltage.
 - one further external analog hardware entropy source based on the infnoise
  design is added.
 - entropy mixing is based on an expert-reviewed public implementation also
  used in the linux kernel.
o (S//CS//NF) Alternatively PITCHFORK also operates as an USB mass
  storage device
 - interfacing with a microSD card slot
 - can be used to host write-protected filesystems.
o (S//CS//NF) PITCHFORK is based on free software and hardware, all
  designs are available for inspection, improvements and sharing.
o (S//CS//NF) PITCHFORK is customizable and extensible
 - comes as a developer version with a JTAG and can be unrevocably locked
   when the user requires it.
 - the simple and free design allows user to customize and
   extend their PITCHFORKS, e.g.:
 - usage of strong cryptography for transparent encryption of
   your boot partitions and FDE key material via the microSD slot.
 - omitting write-functionality for the microSd slot in the firmware,
   provides high confidence in read-only access to stored data.
 - a PITCHFORK could in theory also host a bitcoin Trezor.
 - could also interface with smartphones and provide cryptographic services
 - using the python bindings a gnupg command-line compatible wrapper could
  enable applications like enigmail to transparently use PITCHFORKs.
 - different cases, like a smartwatch form-factor.
 - USB2.0 support,
 - implementing HOTP, TOTP, PFS, anonymous attribute-based credentials,
  password stores.
o (S//CS//NF) PITCHFORKs should be available as
 - conference gadgets
 - nokia3310 replacement board (needs drilling one hole in the nokia case,
  and a LiPo battery)
 - self-assembly kits, at a non-profit price,
 - ready assembled units for NGOs and journalists free of charge/nominal fee
 - ready assembled for commercial orders.
```
