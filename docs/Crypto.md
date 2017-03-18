# Cryptographic specification

The goals of the original design were to attempt to
- get rid of all fingerprintable metadata,
- go full post-quantum,

## Changes to the algorithms

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

The key-exchange [implements](http://l6jhsd5aflooopjj.onion/git/firmware/tree/crypto/kex.c#n138) (kex.c) a post-quantum axolotl extension of the
x3dh protocol. It (currently) omits the AD(pkidA||pkidB)
construct. Instead it mixes in a newhope post-quantum component and
compares the fingerprints in one go on the display.

The x3dh protocol creates the derives the resulting key by performing
3 scalar multiplications on 2 pairs of elliptic keys, it concatenates
the 3 products and hashes them into the final key. The PITCHFORK
[modifies](http://l6jhsd5aflooopjj.onion/git/firmware/tree/crypto/axolotl.c#n70) (axolotl.c) this by introducing a 4th element hashed into the final key
by performing in parallel to the 3 multiplications also a Newhope key
exchange, which produces also a shared secret of 32 bytes, this is
used as a fourth component to the final hash of the x3dh protocol.
This way the PITCHFORK adds a post-quantum component to the
key-exchange. If either one of the underlying schemes (elliptic
curves, RLWE) breaks the other surviving still protects the
confidentiality of the key-exchange.

To thwart MiTM attacks to the key-exchange a 16B validator is derived
from the shared secret, which is displayed using [pgp-words](https://en.wikipedia.org/wiki/PGP_word_list)

## Key storage
## Encryption
## Signatures
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


