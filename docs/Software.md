# PITCHFORK Software

This page attempts to document everything related to the software of the PITCHFORK.

In this project the term "software" pertains to everything running on
the other end of the USB connection. Currently this means a personal
computer, but in the future hopefully also on phones.

## Hardware Requirements

- a PITCHFORK
- a USB cable
- a host
- the [host library](../git/software)

## How to use PITCHFORK on the CLI

This front-end (and the library) are even more experimental than the
rest of the project. Please contribute, or allow some time to
fix/finish things up here.

### Initiate a key-exchange

```
./pitchfork kex >/tmp/kex
```

### Respond to a key-exchange request

```
./pitchfork respond Alice </tmp/kex >/tmp/response
```

### Finish key-exchange request

```
./pitchfork end Bob </tmp/response
```

### XEDDSA signing

```
echo "sign me" | ./pitchfork sign >/tmp/signature
```

### XEDDSA signature verification

```
{cat /tmp/signature; echo "sign me" } | ./pitchfork verify Bob
```

### shared key encryption

```
echo 'PITCHFORK!!5!' | ./pitchfork encrypt Bob >/tmp/cipher && ./pitchfork decrypt Bob </tmp/cipher
```

### Sending axolotl (signal) message

```
echo '1<3u' | ./pitchfork send Bob >/tmp/ciphertext
```

### Receiving axolotl (signal) message

```
./pitchfork recv Alice </tmp/ciphertext
```

### Signing with sphincs
**currently broken**
```
echo "sign me" | ./pitchfork pqsign >/tmp/pqsign
```

### Verifying sphincs signatures
**currently broken**

This operation does not need a connected PITCHFORK. However it needs
the signers public key. In this example the signers public key is
stored in a file called `pqbob`:

```
{ cat pqbob; cat /tmp/pqsign; echo "sign me"} | ./pitchfork pqverify
```
