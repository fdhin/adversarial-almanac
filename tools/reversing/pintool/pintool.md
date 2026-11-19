---
name: "pintool"
categories:
  - blackarch-reversing
version: "24.d538a79"
homepage: "https://github.com/wagiro/pintool"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "wagiro/pintool"
github-stars: 145
github-pushed: "2020-01-30"
github-archived: false
github-issues: 3
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# pintool

> Recovers serials and keys via an instruction-count side channel, automating the brute force on top of Intel PIN.

## What it's for

pintool (by wagiro/Eduardo García) is a Python wrapper around Intel PIN's `inscount0` instruction-counting tool. It implements the "count me if you can" technique: many key-checking routines execute a measurably different number of instructions for each additional correct character, so by counting instructions over candidate inputs the checker itself leaks the right character at each position. pintool automates the whole character-by-character recovery — run the target under PIN, read the counter, keep the character whose count matches the success delta, move to the next position. It needs an Intel PIN toolkit on the host, with its path set in the variables at the top of the script, and it only works on checkers whose instruction count actually correlates with input correctness. The improved fork [[pintool2]] adds reverse-order recovery.

## Typical use cases

### Recover a key character by character

Let the instruction counter reveal each accepted character.

```
python3 pintool.py -l 30 -c 1,2,3 -b _{} -s - ./crackme
```

Outcome: brute-forces each of the 30 positions over lowercase, uppercase and digits (`-c 1,2,3`) plus the extra characters `_{} `, using `-` as the placeholder filler; prints one line per accepted character with its instruction-count delta and finishes with the full key on its own line.

### Constrain the search with a known prefix and delta

Skip what you already know and match the checker's behavior.

```
python3 pintool.py -l 37 -c 4 -i 'KEY{' -b }_ -s - -d '=> 651' ./crackme
```

Outcome: starts the recovery from the known `KEY{` prefix, brute-forces only hexadecimal characters (`-c 4`), and treats a per-character delta of at least 651 instructions as the success signal (`-d`) instead of the default `!= 0` — for checkers whose counter moves in a specific direction or amount.

### Probe the key length first

Find how long the input is before the full search.

```
python3 pintool.py -e -l 40 ./crackme
```

Outcome: studies how instruction counts vary across candidate lengths up to 40, identifying the length at which the checker's behavior changes before committing to a full run. `-a 32`/`-a 64` selects the matching PIN counting module for the target's architecture.

*No corpus evidence — derived from upstream documentation.*
