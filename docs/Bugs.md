# Bugs

The following functionality does not work correctly:

## Proper handling of key IDs in key-exchange with host library

The participant names in the key-exchange should be part of the
exchange (and also of the AEAD), and not explicit on the command line.

## Pad encrypted blocks

To mitigate truncation attacks the encryption needs to be padded.

## Hardware bugs documented in the the hardware sections

These bugs are documented in their respective sections:

- [USB token](usb_token/#bugs-in-the-latest-revision)
- [Nokia 3310](nokia/#bugs-in-the-latest-revision)

## Legal issues

see the [licensing issues](Copyright)
