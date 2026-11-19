---
name: "princeprocessor"
categories:
  - blackarch-misc
version: "132.bffda8c"
homepage: "https://github.com/jsteube/princeprocessor/"
packaged-by: [blackarch]
blackarch-builddate: "2018-07-08"
build-system: make
license: MIT
meta-checked: "2026-11-09"
github-repo: "jsteube/princeprocessor"
github-stars: 501
github-pushed: "2023-10-30"
github-archived: false
github-issues: 9
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# princeprocessor

> Standalone password candidate generator implementing the PRINCE attack, designed to be piped into hashcat or another cracker.

## What it's for

PRINCE (PRobability INfinite Chained Elements) is best thought of as an advanced combinator attack: instead of combining two separate wordlists, it recombines words from a single input wordlist into "chains" of one to N concatenated words whose lengths add up to the target length. princeprocessor, by Jens Steube (the author of hashcat), generates those candidates and streams them to stdout, so it is almost always used as the left side of a pipe feeding a cracker. It does no hashing itself — it is purely the candidate-generation half of an attack. The BlackArch package installs the 64-bit build (upstream `pp64.bin`) as `/usr/bin/princeprocessor`.

## Typical use cases

### Feed PRINCE candidates straight into hashcat

```
princeprocessor words.txt | hashcat -a 0 -m 0 target.hash
```

Outcome: reads words.txt, emits chained candidates continuously, and hashcat consumes them from stdin against the hashes in target.hash — no intermediate candidate file is ever written.

### Restrict candidate length

```
princeprocessor --pw-min=8 --pw-max=12 words.txt
```

Outcome: prints only candidates between 8 and 12 characters, matching a target's known password policy.

### Estimate the keyspace before committing

```
princeprocessor --keyspace words.txt
```

Outcome: computes and prints the number of combinations the current options will generate, then exits without emitting any candidates — use it to size a run before starting it.

### Add case permutation

```
princeprocessor --case-permute words.txt
```

Outcome: for every word that begins with a letter, also emits a variant with the opposite case of the first letter (password/Password), covering capitalization habits for near-zero extra effort.

### Use the wordlist's own length distribution

```
princeprocessor --wl-dist-len words.txt
```

Outcome: derives the output length distribution from the input wordlist's statistics instead of the built-in table, which typically raises hit rates on human-chosen passwords.

### Slice a run for distributed cracking

```
princeprocessor -s 2400000000 -l 1200000000 words.txt
```

Outcome: skips the first 2.4 billion candidates and stops after the next 1.2 billion, giving each instance in a distributed run a disjoint slice of the same stream; without -l it simply resumes from that offset.

*No corpus evidence — derived from upstream documentation.*
