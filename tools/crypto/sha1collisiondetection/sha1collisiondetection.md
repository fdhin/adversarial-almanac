---
name: "sha1collisiondetection"
categories:
  - blackarch-crypto
version: "106.a06f1ad"
homepage: "https://github.com/cr-marcstevens/sha1collisiondetection"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2025-11-10"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "cr-marcstevens/sha1collisiondetection"
github-stars: 1424
github-pushed: "2025-10-30"
github-archived: false
github-issues: 18
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# sha1collisiondetection

> SHA-1 hashing utilities and library that also detect whether a file was crafted with a cryptanalytic SHA-1 collision attack (SHAttered, SHA-Mbles) — a near drop-in replacement for sha1sum.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Marc Stevens and Dan Shumow's counter-cryptanalysis tooling: `sha1dcsum` computes the SHA-1 hash of files exactly like sha1sum, but additionally detects — with probability 1, false-positive rate below 2^-90 — whether the input was generated using any of the top 32 SHA-1 disturbance vectors, i.e. a deliberate collision attack such as the shattered.io PDFs or the later SHA-Mbles attack. It costs less than twice the runtime of plain SHA-1. Use it to audit repositories of legacy SHA-1 checksums or signatures before trusting them: a matching hash alone no longer proves integrity now that SHA-1 collisions are practical. The companion `sha1dcsum_partialcoll` detects collisions against reduced-round SHA-1, for analysis of near-collision research artifacts. The same detection ships as a C library with a "safe-hash" mode: when a collision is detected it returns a different, unpredictable hash, which makes SHA-1-collision signature forgeries fail automatically in linked applications. It detects full collisions in single files; it is not a general file-integrity checker and does not detect attacks against other hash functions.

## Typical use cases

### Hash files while screening for collision attacks

```
sha1dcsum release.tar.gz legacy-signature.bin
```

Outcome: prints `hash  filename` lines like sha1sum, but any file built with a cryptanalytic SHA-1 collision attack triggers an explicit collision warning naming that file, instead of silently vouching for a forged document.

### Screen data from a pipe

```
cat suspicious.pdf | sha1dcsum -
```

Outcome: hashes standard input (the `-` argument) with the same collision detection as for files, so streamed or decompressed content can be checked without writing a temporary file.

### Analyze reduced-round collision artifacts

```
sha1dcsum_partialcoll research-nearcoll.bin
```

Outcome: runs the variant detector tuned for cryptanalytic collisions against reduced-round SHA-1, flagging research artifacts (near-collisions) that the full-collision detector would not treat as a practical attack on real SHA-1.
