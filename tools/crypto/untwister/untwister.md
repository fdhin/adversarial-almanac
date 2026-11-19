---
name: "untwister"
categories:
  - blackarch-crypto
version: "119.a42b8f8"
homepage: "https://github.com/altf4/untwister"
packaged-by: [blackarch]
blackarch-builddate: "2016-09-17"
build-system: make
license: GPL3
meta-checked: "2026-11-09"
github-repo: "altf4/untwister"
github-stars: 390
github-pushed: "2018-09-17"
github-archived: false
github-issues: 20
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# untwister

> Multi-threaded seed recovery for glibc rand(), Mersenne Twister, PHP/Ruby MT variants and Java Random.

## What it's for

untwister recovers the seed of a pseudo-random number generator from a sample of its output. Feed it the observed results of glibc's `rand()`, MT19937, PHP's `mt_rand()`, Ruby's `rand()` or Java's `Random()` and it first attempts a state-inference attack, then falls back to multi-threaded brute force over a seed range — including the classic weak case of applications seeded with `time()`. It also generates test output from a known seed, so you can validate a recovery before trying it on real data. It works on newline-separated 32-bit integers; bounded generators (e.g. `rand() % 100`) are handled by giving the output bounds so candidates can be re-scaled. It does not attack generators whose internal state is wider than a 32-bit seed or cryptographic PRNGs.

## Typical use cases

### Recover a seed from observed MT19937 output

```
untwister -i observed.txt -r mt19937
```

Outcome: reads newline-separated integers from `observed.txt`, tries state inference and then brute force against Mersenne Twister, and prints each candidate seed with a confidence percentage. Re-generate the sequence from the reported seed to confirm it reproduces every observed value; default depth is 1000 draws per candidate.

### Crack a timestamp-seeded generator

```
untwister -i observed.txt -r php-mt_rand -u
```

Outcome: brute-forces only Unix timestamp values within ±1 year of the current time — the typical seeding pattern of `srand(time())` web apps — which reduces the search from the full 64-bit space to a few tens of millions and usually finishes in minutes.

### Bound the brute-force range and thread count

```
untwister -i observed.txt -r glibc-rand -s 0 -S 1000000 -t 8
```

Outcome: scans seeds 0 through 1000000 inclusive on 8 threads; `-c` raises the minimum confidence reported so weak partial matches are filtered out.

### Generate a test set from a known seed

```
untwister -g 1337 -r mt19937 -D 50 > observed.txt
```

Outcome: prints 50 integers produced by MT19937 seeded with 1337; feed the file back to `-i` to verify the recovery workflow end to end before using it on captured data.

*No corpus evidence — derived from upstream documentation.*
