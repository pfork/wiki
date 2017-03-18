# Bugs

The following functionality does not work correctly:

## Firmware signature verification

After some recent rewrite of the crypto ops, this was not working and had to be disabled as a workaround.

## Sphincs signatures

Signatures made by the pitchfork cannot be verified by the host library.

## Proper handling of key IDs in key-exchange with host library

The participant names in the key-exchange should be part of the
exchange (and also of the AEAD), and not explicit on the command line.

## Pad encrypted blocks

To mitigate truncation attacks the encryption needs to be padded.

## Firmware update does not provide user feedback

Simple UI fix necessary.

## Hardware bugs documented in the the hardware sections

These bugs are documented in their respective sections:

- [USB token](usb_token/#bugs-in-the-latest-revision)
- [Nokia 3310](nokia/#bugs-in-the-latest-revision)
