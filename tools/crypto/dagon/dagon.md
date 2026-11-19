---
name: "dagon"
categories:
  - blackarch-crypto
version: "244.f065d7b"
homepage: "https://github.com/Ekultek/Dagon"
packaged-by: [blackarch]
blackarch-builddate: "2018-11-14"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "Ekultek/Dagon"
github-stars: 175
github-pushed: "2019-10-14"
github-archived: false
github-issues: 18
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# dagon

> Python hash-identification and brute-forcing workbench with built-in dictionary generation and salting.

## What it's for

Dagon (by Ekultek, named after the prince of Hell) is an advanced hash cracking and manipulation system: it brute-forces multiple hash types, builds brute-force dictionaries on the fly (a built-in wordlist creator generates roughly 100,000 candidate strings when none is supplied), automatically verifies which hashing algorithm produced a given hash, and generates random salts from Unicode to ASCII. It runs as a Python 2.7 script (`dagon.py`), works on a single hash or on whole files of hashes one per line, and is a standalone single-host cracker — not a distributed or GPU cracker. Upstream development stopped in 2019.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Identify the algorithm behind a hash

```
python dagon.py -v 5f4dcc3b5aa765d61d8327deb882cf99
```

Outcome: prints which hashing algorithm(s) Dagon judges capable of having produced the digest — the triage step before choosing how to crack it; pass `-L` to view all possible algorithms, including ones not yet implemented for cracking.

### Brute-force a single hash

```
python dagon.py -c 5f4dcc3b5aa765d61d8327deb882cf99 --bruteforce
```

Outcome: attempts to recover the plaintext of the hash by brute force; when no wordlist is supplied the built-in generator first creates ~100,000 candidate strings and the tool then cracks against them, reporting the plaintext when found.

### Brute-force a file of hashes

```
python dagon.py -l hashes.txt --bruteforce
```

Outcome: runs the same brute-force/dictionary pipeline over every hash in the file (one per line), reporting recovered plaintexts per line.

### Identify algorithms across a whole hash dump

```
python dagon.py -V hashes.txt
```

Outcome: runs algorithm verification against each hash in the file in turn — useful bulk triage before splitting a dump into per-format cracking runs.
