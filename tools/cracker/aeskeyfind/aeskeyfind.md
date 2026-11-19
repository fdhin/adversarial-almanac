---
name: "aeskeyfind"
categories:
  - blackarch-cracker
version: "1.0"
homepage: "http://citp.princeton.edu/memory/code/"
packaged-by: [blackarch, kali, aur, nixpkgs]
blackarch-builddate: "2018-11-29"
build-system: make
license: BSD
meta-checked: "2026-11-09"
github-repo: "makomk/aeskeyfind"
github-stars: 70
github-pushed: "2017-05-11"
github-archived: false
github-issues: 2
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# aeskeyfind

> Locates 128- and 256-bit AES keys inside raw memory images by recognising key-schedule structure.

## What it's for

AESKeyFinder (Heninger & Feldman, Princeton CITP, 2008) scans a captured memory image for blocks whose bytes satisfy the mathematical constraints of an AES key schedule and prints every 128- or 256-bit key found. Because it keys on structure rather than exact byte patterns, it still finds keys when several bits have decayed. Run it on full RAM captures, VM memory files, core dumps or hibernation files, and pair it with aesfix to repair heavily decayed schedules. It does not decrypt anything itself — it recovers key material for use elsewhere.

## Typical use cases

### Scan a memory image for AES keys

```
aeskeyfind memory.img
```

Outcome: shows a progress bar while scanning, then for each candidate prints "FOUND POSSIBLE 128-BIT KEY AT BYTE <offset>" followed by the key in hex.

### Tolerate more RAM decay

```
aeskeyfind -t 25 memory.img
```

Outcome: raises the maximum tolerated bit errors per candidate schedule (default 10), recovering keys from more corrupted dumps at a slightly higher false-positive risk.

### Emit the full schedule for repair by aesfix

```
aeskeyfind -v -q memory.img
```

Outcome: prints each key with its EXTENDED KEY (the full key schedule) and the schedule constraint rows, without the progress bar; save the extended key as hex and pass it to aesfix to correct residual bit errors.

*No corpus evidence — derived from upstream documentation.*
