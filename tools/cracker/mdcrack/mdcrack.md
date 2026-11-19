---
name: "mdcrack"
categories:
  - blackarch-cracker
version: "1.2"
homepage: "http://c3rb3r.openwall.net/mdcrack/"
packaged-by: [blackarch, aur]
blackarch-builddate: "2016-01-13"
build-system: make
license: custom:unknown
meta-checked: "2026-11-09"
worth: 0.8
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# mdcrack

> CPU brute-forcer for MD4, MD5 and NTLMv1 hashes, with resumable sessions and precomputable hash dumps.

## What it's for

MDCrack (Gregory Duchemin) recovers plaintexts from unsalted MD4, MD5 and NTLMv1 hashes using optimized cracking cores and an incremental charset search. It is aimed at single hashes grabbed off the wire or out of Windows SAM dumps (NTLMv1 = the NT hash format those tools emit), and it can also dump every computed hash to a file (`-W`) for later egrep-based collision searches. It has no GPU support and no salted-hash formats — for anything modern, hashcat/john are the right tool; MDCrack's niche is tiny, legacy, unsalted targets. Sessions survive interruption: rerunning with no options resumes where the last run stopped.

## Typical use cases

### Crack a single MD5 hash

```
mdcrack -M MD5 098f6bcd4621d373cade4e832627b4f6
```

Outcome: performs an incremental charset search and prints the found collision (the plaintext) for the digest; MD4 works identically with `-M MD4`.

### Attack an NTLM hash from a SAM dump

```
mdcrack -M NTLM1 31d6cfe0d16ae931b73c59d7e0c089c0 -S 4
```

Outcome: brute-forces the NT hash in NTLM1 mode; `-S 4` starts the search at password length 4 to skip useless short candidates.

### Constrain the charset and find every collision

```
mdcrack -s abcdef0123456789 -S 4 -a -M MD5 098f6bcd4621d373cade4e832627b4f6
```

Outcome: searches only the supplied charset string from length 4 up, and `-a` keeps going after the first plaintext, listing all collisions in range instead of stopping.

### Resume an interrupted session

```
mdcrack
```

Outcome: with no options at all, the tool reloads its resume file and continues the previously stopped search from where it left off.

*No corpus evidence — derived from upstream documentation.*
