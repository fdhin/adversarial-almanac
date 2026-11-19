---
name: "aesfix"
categories:
  - blackarch-cracker
version: "1.0.1"
homepage: "http://citp.princeton.edu/memory/code/"
packaged-by: [blackarch, kali, aur, nixpkgs]
blackarch-builddate: "2024-02-04"
build-system: make
license: BSD
meta-checked: "2026-11-09"
github-repo: "Seabreg/aesfix"
github-stars: 1
github-pushed: "2019-05-04"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# aesfix

> Repairs bit-flip damage in an AES-128 key schedule recovered from a cold-boot memory dump.

## What it's for

aesfix implements the key-schedule error-correction step of the Princeton cold-boot research (Heninger & Halderman, "Lest We Remember: Cold-Boot Attacks on Encryption Keys", USENIX Security 2008). When RAM decay has corrupted a hex-encoded AES-128 key schedule pulled from a memory image, it enumerates possible decodings of each damaged byte slice and outputs the schedule that could have decayed into the observed one. Feed it the extended-key output of a key finder such as aeskeyfind (`aeskeyfind -v`). Limitations: AES-128 schedules only, and only unidirectional 1→0 bit errors — the typical signature of DRAM decay after a cold reboot, not arbitrary corruption.

## Typical use cases

### Repair a decayed AES-128 key schedule

```
aesfix key-schedule.hex
```

Outcome: prints "decoding for weight N ..." progress lines while widening the error search, then the corrected key schedule as rows of hex words; the first row is the recovered 128-bit key, ready for use in any tool that accepts a raw AES-128 key.

*No corpus evidence — derived from upstream documentation.*
