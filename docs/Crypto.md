# Cryptographic specification

The goals of the original design were to attempt to

- get rid of all fingerprintable metadata,
- go full post-quantum,
- support forward secrecy modes,
- provide means to stay anonymous,
- use small public keys that can be shared even in tweets,
- provide signatures for software packages.

## Changes to the algorithms

Although we tried to avoid to invent an new crypto, in some cases
changes where necessary, in some other cases we failed marginally and
invite reviews to validate the "inventions".

### One hash

Some algorithms used in the PITCHFORK used some variant of SHA, some
used Blake. Due to the limited size of the firmware it was necessary
to consolidate into the one provided by libsodium:
crypto_generichash - Blake2b.

### ChaCha20

All uses of ChaCha12 have been replaced by a ARM thumb assembly
optimized version of ChaCha20.

## Master key Key derivation

This is a tricky one, a strong KDF should both be space- and
time-resistent, however the Cortex-M is neither very fast nor has it
plenty of RAM. The recommended (and used) solution is a generichash-based (blake2b)
[implementation](../git/firmware/tree/crypto/pbkdf2_generichash.c) of PBKDF2 (SP 800-132).

## Key exchange

The key-exchange implements a post-quantum axolotl extension of the
X3DH protocol. It (currently) omits the AD(pkidA||pkidB)
construct. Instead it mixes in a newhope post-quantum component and
compares the fingerprints in one go on the display.

The original [X3DH](https://whispersystems.org/docs/specifications/x3dh/) protocol creates the derives the resulting key by
performing 3 scalar multiplications on 2 pairs of elliptic keys, it
concatenates the 3 products and hashes them into the final key.

The PITCHFORK
[modifies](http://l6jhsd5aflooopjj.onion/git/firmware/tree/crypto/axolotl.c#n70) (axolotl.c/triple_dh)
this by introducing a 4th element hashed into the final key by
performing in parallel to the 3 multiplications also a New Hope
post-quantum key exchange, which produces also a shared secret of 32
bytes, this is used as a fourth component to the final hash of the
X3DH protocol.  This way the PITCHFORK adds a post-quantum component
to the key-exchange. If either one of the underlying schemes (elliptic
curves, RLWE) breaks the other surviving still protects the
confidentiality of the key-exchange.

To thwart MiTM attacks to the key-exchange a 16B validator is derived
from the shared secret, which is displayed using [pgp-words](https://en.wikipedia.org/wiki/PGP_word_list)

## Key storage

Key material is stored using [cwrite](http://l6jhsd5aflooopjj.onion/git/firmware/tree/crypto/pf_store.c#n83),
a simple function encrypting it's input using the master key queried
from the PITCHFORK owner and run through the generichash-based PBKDF2.

## Encryption

Three encryption modes are provided in [pitchfork.c](http://l6jhsd5aflooopjj.onion/git/firmware/tree/crypto/pitchfork.c):

- simple shared secret-based encryption using crypto_secretbox,
- uni-directional encryption for whistle-blowing, leaking,
- [Double ratchet](https://whispersystems.org/docs/specifications/doubleratchet/)-based messaging as in the Signal Protocol

### Simple crypto_secretbox encryption

This mode is keyed by a shared-secret and is otherwise used by the
other two encryption modes. Encryption is done in 32KB blocks,
without padding the last block (which is a TODO), the nonce is
incremented after each block to mitigate against reordering attacks.

The output of this encryption is a 24B random nonce, the cyphertext
and the MAC which is 16B as is standard with crypto_secretbox. All of
this should have maximum entropy and if not "enriched" with metadata
should not be possible to cheaply fingerprint.

### Whistleblowing mode

In this mode the sender doesn't need a PITCHFORK the host software in
combination with the recipients public key is enough. The sender
generates an ephemeral key, using a simple ECDH it generates a shared
secret with the recipients public key and using this secret encrypts
the plaintext, which is sent together with the 24B nonce, the 32B
ephemeral public key, the ciphertext and the 16B MAC.

The recipient needs its PITCFORK containing the used public key,
reverses the above procedure to decrypt the message.

### Double Ratchet messaging

This the Double Ratchet itself is extracted into `ax_(recv|send)_init` in [crypto/pitchfork.c](../git/firmware/tree/crypto/pitchfork.c)

## Signatures

Two signature schemes are supported:

- [XEDDSA](https://whispersystems.org/docs/specifications/xeddsa/)
- [Sphincs](https://sphincs.cr.yp.to/index.html)

### XEDDSA
The implementation can be found in the [git repo](../git/xeddsa/),
currently it is a direct descendant of libsignal-c.

### Sphincs
The [implementation](../git/pitchforkedsphincs/) has been modified
from the [original armedsphincs](https://eprint.iacr.org/2015/1042) by
the TU of Nijmegen by

- unrolling the 3 phases back into one consecutive memory layout,
- [replacing chacha12 with chacha20](../git/pitchforkedsphincs/commit/?id=596a98736e33795fc06f2b5478274cefa6719ea4),
- and by [replacing the internal Blake](../git/pitchforkedsphincs/commit/?id=97d6223a051d6c15a9a5e4cabe6f8565c58c2c60) by the system-wide generichash-based one.

## Firmware signature

The firmware signature is based on the crypto_sign functionality and
is currently borken and awaiting to be rewritten by an XEDDSA
signature.

## Random number generator

The PITCHFORK comes with a strong random number generator. It consists
of 3 parts:
- 3 independent entropy sources
- entropy mixer/pool
- pseudo-random function

Random bytes
are [generated](../git/firmware/tree/crypto/randombytes_pitchfork.c)
by `crypto_stream_salsa20`, which is keyed by the entropy pool.

The entropy pool/mixer [implementation](http://l6jhsd5aflooopjj.onion/git/firmware/tree/crypto/mixer.c) is taken from the linux kernel,
it mixes three independent entropy sources:

- the STM32F2xx [internal RNG](http://l6jhsd5aflooopjj.onion/git/firmware/tree/core/rng.c) - providing 32bits of entropy - if needed - every 40 cycles
- Two miscalibrated ADCs reading [reference voltage](../git/firmware/tree/core/adc.c#n103) and [internal temperature](../git/firmware/tree/core/adc.c#n91).
- An [external entropy](../git/firmware/tree/core/xentropy.c) source based on [infnoise - The world's easiest TRNG to get right](https://github.com/waywardgeek/infnoise)


