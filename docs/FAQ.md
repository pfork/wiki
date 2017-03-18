# PITCHFORK!!5! FAQ

Understanding your PITCHFORK is essential: technology which is not understood, controls the user.

## What exactly is the threat model of the PITCHFORK?
We have a [wikipage](Threatmodel.md) on this (which you should definitely read!), but to cut things short the mitigated threat-vector is recovering your keys from your laptop or your smartphone. And it allows you to keep physical contact with all your keys and secrets, while for example even going to a sauna and trying to maintain physical contact with your keys.

## How is this different from my yubikey neo/cryptostick/nitrokey/etc?
The PITCHFORK uses a powerful ARM Cortex-M3 processor for all operations, while the tools mentioned all are based on smartcards.

## Why not use a smartcard based design, if everyone does that?
The code in smartcards is not transparent. You cannot know what kind of backdoors and bugs are in there.

## But smartcards are better protected against physical attacks.
They are. But the primary attack vector the PITCHFORK defends against is remote key recovery malware on your PC/ Smartphone not defending against physical attacks, you should always maintain physical contact with your PITCHFORK or assume it being compromised.

Against most adversaries the protections provided by the ARM CPU for protecting the intellectual property of embedded systems vendors from foreign copycats should be enough especially if invasive physical attacks are not possible without detection.

Consider this CPU in general a target for all kind of reverse engineering of successful products for unlicensed reproduction. For breaking those there's a constant and profitable potential, if such protected firmware gets copied, it's probably from the company source code servers, or public firmware updates, than from reading it out from locked down micro controllers.

For the adversaries who can circumvent the protection of the PITCHFORK it should be also possible to circumvent the protections of a smartcard. If Chris Tarnovsky can do it, there's probably a few labs that you have never heard of but have similar capacities.

## How is this different from my gnuk?
it's got a few more features, but the baseline should be the same, with small modifications it should be possible to burn the gnuk firmware onto a PITCHFORK

## Hey, can I run my Tresor bitcoin wallet on it?
The hardware is quite similar, it should be possible with small modifications.

## How is this different from my SC4-HSM?
The SC4-HSM does have a simpler design than the PITCHFORK, it lacks an external entropy source, the buttons are not suitable to enter any input to derive cryptographic strength keys for encryption of keys in flash. The PITCHFORK also contains an micro-sd card reader and most importantly it allows key exchanges over the integrated radio interface.

## How is the keys protected from reading out of the PITCHFORK?
Against remote software-based attacks the PITCHFORK has a minimal attack surface, and each operation on keys needs a phyiscal contact with the PITCHFORK by pressing a button. Memory protections prohibit code in RAM to execute.

Against physical attacks the locked down PITCHFORK has it's Flash Protection Level 2 fuses burnt, which disables JTAG and the other debug facilities and disables all access to the firmware except the CPU data and instruction buses when booted from the firmware. This should make the reading out of the flash as difficult as it is for smartcards. Glitching might remain as a non-invasive attack-vector, otherwise the Flash Protection fuse needs to be reset somehow.

Even if the flash can be read out, it is all encrypted derived by a key entered from the PITCHFORK buttons and some secret stored in the PITCHFORK option bytes.

Locked-down PITCHFORKs can also have a coating of epoxy covered with glittered nail-polish and a saved picture of the glitter for comparison later.
