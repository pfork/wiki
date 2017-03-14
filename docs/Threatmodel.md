# Threat model

PITCHFORK directly protects cryptographic key material from
adversaries controlling the users environment such as network
traffic, general computing devices, possession of the key
container. The concept is similar to smartcard-based cryptographic
key-compartmentalization devices like the cryptostick, however due
to different capabilities having also different attack vectors.

## Adversary capabilities

The adversary is actively intercepting traffic, doing traffic
analysis and has control of victims computer/phone using
malware/rootkits (e.g. finfisher or hacking team). Adversary
stores all cryptograms. When a person of interest is identified
the adversary takes control on the PoI computers/phone using some
malware and recovers any cryptographic keys exposed.

In case of persons of high interest also temporary and physical
access to all possessions of the victim are possible (such as
theft, confiscation, evil maid attacks, etc)

Besides knowledge about possible very generic and low-level
backdoors already existing in CPU and other hardware components,
the adversary is able to interdict and compromise specific
shipments of components to include tailored backdoors.

Furthermore it's possible that attackers aquire possibilities to mount
post-quantum attacks against traditional crypto algorithms.

Assuming the existance and interest of the above described
attacker a PITCHFORK attempts to mitigate against the following
attack vectors derived from the adversary capabilities:
   - key recovery from a general purpose computing device
   - traffic analysis based on metadata in cryptograms
   - key compromise during short-term physical access by the adversary
   - backdooring during production and shipment
   - resist post-quantum attacks on cryptographic algorithms

By shifting the key material from a general computing device to a
key compartmentalization device, we also shift and reduce the
attack surface from the general to the specific device. We
identified the following attack vectors, deployed or planned
mitigations against them and make assumptions regarding the
capabilities or costs of the adversary:

## Production & Assembly

- Attack Vector: device is backdoored in production or during
shipment.
- Adversary Capability: assumed to be available to the adversary
and moderately expensive.
- Mitigation: The device is simple enough to allow possible users
to buy all components in their local store and build them using
simple tools.
- Impact: possibly full compromise of all keys

## Attacks from the host

### Malwared host leaks plaintext
- Attack Vector: the host device (smartphone, PC, laptop, etc) is
  backdoored and malware is realtime leaking plaintext before or
  after encryption on the PITCHFORK
- Adversary Capability: assumed to be available to the adversary
  and moderately expensive: the continous presence on the victim
  host is raising the probability of discovery, and in case of
  classified malware the public availability of it.
- Mitigation: none directly, the PITCHFORK does not protect
  against this. Additional defense-in-dept measures like proper
  air-gapping the host can however mitigate against such threats.
- Impact: compromise of plaintext messages seen on compromised host
  device.

### Malwared host doing power analysis
- Attack Vector: the host device (smartphone, PC, laptop, etc) is
  backdoored and malware is attacking the PITCHFORK over the USB
  interface attempting to recover key material using power
  analysis attacks.
- Adversary Capability: assumed to be mostly unavailable to the
  adversary not many usb ports have power consumption measurement
  capabilities. Being able to to measure consumption otherwise
  assumes physical access which is handled later in this document
- Mitigations: the PITCHFORK is powered by a battery, USB is only used
  for charging the battery, thus the battery buffers out any
  measureable consumption information.
- Impact: possibly full compromise of one or more keys.

### Malwared host attacking for code execution
- Attack Vector: the host device (smartphone, PC, laptop, etc) is
  backdoored and malware in full knowledge of the free software
  source of the PITCHFORK is attacking the PITCHFORK over the USB
  interface attempting to gain code execution on the PITCHFORK.
- Adversary Capability: assumed to be available to the adversary
  and depending on mitigations can be quite expensive.
- Mitigations:
    - keeping the keymaterial encrypted in the flash, only unlocked
      when needed, limiting the window of opportunity for the
      attacker.
    - keeping te the USB interface as small and simple as possible.
    - marking all RAM non-executable using the Memory Protection
      Unit (MPU) of the processor,
    - using gcc stackprotector,
    - making firmware read-execute-only using fuses,
    - code reviews,
    - avoid usage of function pointers
    - Other defense-in-depth measures - like always removing the
      PITCHFORK from the USB port when not neccessary.
- Impact: possibly full compromise of one or more keys.

### Backdoored SDcard firmware attacking for code execution
- Attack Vector: backdoored SD card firmware attempting to gain
  code execution on the PITCHFORK.
- Adversary Capability: limited availability to the adversary and
  difficult as it needs to be delivered to and used by the victim.
- Mitigations: same as USB interface code-exec mitigations
- Impact: possibly full compromise of one or more keys.

### Malicious filesystem on SDcard attacking for code execution
- Attack Vector: malicious FATFS image on an SD card attempting to
  gain code execution on the PITCHFORK.
- Adversary Capability: assumed to be possible to develop for the
  adversary but difficult as it needs the victim to use the
  backdoored file system.
- Mitigations: same as USB interface code-exec mitigations
- Impact: possibly full compromise of one or more keys.

## Local access

### Attempt to gain code-execution using radio transmission
- Attack Vector: an attacker in radio range can attempt to gain
  code execution via crafted packages.
- Adversary Capability: assumed to be possible to develop for the
  adversary but assumed difficult as the attack surface is
  extremely small and needs close proximity to victim.
- Mitigations: same as USB interface code-exec mitigations
- Impact: same as USB interface code-exec impact

### Sniff key-exchange
- Attack Vector: an attacker in radio range can attempt to
  passively capture radio emissions to recover key material.
- Adversary Capability: assumed to be possible for the adversary
  but moderately expensive as it needs close proximity to the victim.
- Mitigations: use shielding around the complete device except the
  usb port. This doesn't work quite when doing key exchange over
  radio. Seperate physical power-switch for the radio components,
  to disable that part of the device reliably.
- Impact: possibly full compromise of one or more keys.

### Attack key-exchange using quantum cryptographic attacks
- Attack Vector: an attacker in radio range can attempt to
  passively capture key exchange messages to attack the public
  parameters with quantum computing.
- Adversary Capability: assumed to be possible in a few years future
- Mitigations: RWE-based post-quantum key exchange provided in
  PITCHFORK.
- Impact: can recover exchanged shared secret when quantum computers
  enable the attacker to do so in a few years time

### Actively man-in-the-middle attack a key exchange
- Attack Vector: an attacker in radio range can attempt to actively
  MiTM attack a key exchange
- Adversary Capability: assumed to be possible for the adversary
  but moderately expensive as it needs close proximity to the victim.
- Mitigations: compare between the peers a derived hash from the
  shared secret and compare it using the display creating a second
  channel that also needs to be controlled by the attacker. MitM
  attacking persons face-to-face comparing some data is considered
  not possible currently.
- Impact: possibly full compromise of shared secret.

### Physical access to a locked PITCHFORK
- Attack Vector: attacker has physical access to a PITCHFORK for
  which the master-key is not available
- Adversary Capability: assumed to be possible for the adversary
- Mitigations:
  - all keys encrypted with an appropriately long master secret
    which is input via buttons
  - epoxy blocking covert/time-limited access to hardware pins
  - disabling of JTAG port
  - locking all GPIO configs after setup (ref man, chp 6.3.6.)
  - firmware flash locked down to level 2, disabling most read
    operations using debug features.
  - water-proof container so that the owner can maintain continous
    physical contact, even for example when going to a sauna.
- Impact: possible compromise of encrypted keys.

### Audio side-channel attack on master-key entry
- Attack Vector: an attacker in audio range can attempt to record the
  clicks the buttons make when entering a passcode.
- Adversary Capability: assumed to be possible to develop for the
  adversary but assumed difficult as the attack surface is
  extremely small and needs close proximity to victim.
- Mitigations:
- Impact: can possibly recover the master passcode for the PITCHFORK

### Shoulder-surfing for master-key
- Attack Vector: an attacker in visual range can attempt to "shouldersurf"
  the entry of the master passcode.
- Adversary Capability: assumed to be possible for the
  adversary but assumed difficult as the attack surface is
  extremely small and needs close proximity to victim.
- Mitigations: unlock the PITCHFORK blindly in a pocket or bag.
- Impact: can possibly recover the master passcode for the PITCHFORK

## Cryptographic aspects

Confidentiality of the keys and ciphertexts depends on the
security of the xsalsa20 stream cipher.

Integrity of keys and ciphertexts depends on the security of a 16
byte MAC based on xpoly1305.

Integrity of the firmware depends on the security of an ed25519
signature.

Integrity of signed messages should depend on the security of
sphincs post-quantum signing.

Confidentiality of key material during key exchanges should be
protected by curve25519 based ECDH, or by New Hope post-quantum
key exchange.

Entropy is provided by an external entropy source, the CPU
internal RNG, and misconfigured ADCs for internal temperature,
reference voltage and battery voltage.

Bruteforce enumeration attacks are slowed down by pbkdf2 (5000
iterations).

Timing attacks on the cryptographic primitives depends on the
resilience of their implementations. The deployed libsodium is
based on nacl by Daniel Bernstein which had timing side-channels
in its threat model and Peter Schwabes implementation of
curve25519 for the cortex M0 utilizing constant time HW adders.
