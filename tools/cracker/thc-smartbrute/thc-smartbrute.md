---
name: "thc-smartbrute"
categories:
  - blackarch-cracker
version: "1.0"
homepage: "https://www.thc.org/thc-smartbrute/"
packaged-by: [blackarch]
blackarch-builddate: "2015-09-15"
build-system: autotools
license: GPL3
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# thc-smartbrute

> Discovers undocumented and secret APDU commands implemented in a smartcard.

## What it's for

Smartcard applications often ship hidden management commands beyond the standard ISO 7816 instruction set. Talking to a local card through a PC/SC reader, this tool iterates Class (CLA) and Instruction (INS) values to find combinations the card answers as valid, then brute-forces the parameter bytes (P1/P2/P3) for a given CLA/INS pair. It can send CHV1/CHV2 PIN verifications first so hidden commands respond in an authenticated state. It is a black-box discovery tool for cards you can physically access — it neither guesses PINs itself nor interprets the responses beyond "valid/invalid".

## Typical use cases

### Sweep the whole instruction space quickly

Check typical class/instruction values first, then the rest of the space.

```
thc-smartbrute --fastresults
```

Outcome: walks all CLA/INS combinations, prioritizing 0x00, 0x80, 0xA0 (GSM) and other common values; every combination the card does not reject is reported as a valid instruction.

### List only non-standard instructions

Skip the ISO 7816 standard instruction set in the results.

```
thc-smartbrute --undoconly
```

Outcome: same enumeration, but only instructions outside the standard list are printed — a shortlist of potentially undocumented commands worth probing further.

### Brute-force P1/P2 of a known instruction

For a valid CLA/INS pair (e.g. the GSM SELECT FILE), find the accepted parameter bytes.

```
thc-smartbrute --cla 0xA0 --ins 0xA4 --brutep1p2
```

Outcome: enumerates P1/P2 combinations (P1 assumed 0x00) and reports which the card accepts, narrowing down how to call the instruction.

### Brute-force P3 once P1/P2 are known

Complete the argument set for the instruction.

```
thc-smartbrute --cla 0xA0 --ins 0xA4 --p1 0x00 --p2 0x00 --brutep3
```

Outcome: finds the valid P3 (length) values for the fully-specified SELECT FILE call, giving a complete CLA/INS/P1/P2/P3 sequence to replay with a card tool.

### Fuzz after PIN verification

Unlock a card whose hidden commands only respond after CHV1 verification.

```
thc-smartbrute --chv1 1234 --fastresults
```

Outcome: a VERIFY CHV1 APDU carrying PIN 1234 is sent first; the CLA/INS sweep then runs against the authenticated card state instead of failing on access conditions.

*No corpus evidence — derived from upstream documentation.*
