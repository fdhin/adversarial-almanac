---
name: "morxbtcrack"
categories:
  - blackarch-cracker
version: "1.0"
homepage: "http://www.morxploit.com/tools/"
packaged-by: [blackarch]
blackarch-builddate: "2018-10-03"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# morxbtcrack

> Recovers the WIF private key behind a passphrase-derived (brainwallet) Bitcoin address.

## What it's for

MorXBTCrack is a bash proof-of-concept from the "Smashing Bitcoin" brainwallet research: given a target Bitcoin address and a passphrase wordlist, it computes sha256(passphrase) as the private key, wraps it into Wallet Import Format (WIF), derives the matching address with the companion `keyfmt` helper (packaged separately as `morxkeyfmt`), and compares it against the target. A hit prints the WIF key, which is enough to import the funds into any wallet. It checks one address at a time and shells out to `bc`, `xxd` and `keyfmt` per candidate, so it is a reference implementation of the brainwallet weakness rather than a high-throughput cracker.

## Typical use cases

### Recover the key of a known brainwallet address

Test a list of likely passphrases against one address.

```
morxbtcrack 1A1zP1eP5QGefi2DMPTfTL5SLmv7DivfNa passphrases.txt
```

Outcome: re-derives the address for every passphrase while printing a progress counter (`Trying => N`); if a passphrase maps to the target address it prints `Cracked your private key is: <WIF key>` and exits. The printed WIF key can be imported with any wallet's `importprivkey`.

*No corpus evidence — derived from upstream documentation.*
