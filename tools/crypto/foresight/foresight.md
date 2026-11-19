---
name: "foresight"
categories:
  - blackarch-crypto
version: "0.1.r12.g6f48984"
homepage: "https://github.com/ALSchwalm/foresight"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-17"
build-system: python
license: MIT
meta-checked: "2026-11-09"
github-repo: "ALSchwalm/foresight"
github-stars: 193
github-pushed: "2017-05-07"
github-archived: false
github-issues: 4
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# foresight

> Python library plus `foresee` CLI that predict future outputs of common PRNGs (glibc, MSVC, PHP, Java, MySQL, arbitrary LCGs) from prior outputs or a known seed.

## What it's for

Applications still seed tokens and IDs from non-cryptographic PRNGs; foresight inverts them. Give it either a known seed (`-s`) or a run of consecutive outputs (`-o`) and it recovers the generator's state and prints the values it will emit next, for glibc `rand`/`random`/`rand_r`, MSVC `rand`, PHP `rand` (Linux or Windows builds), Java `java.util.Random` (`nextInt`/`nextLong`), MySQL `RAND()`, and any linear congruential generator you can parameterize. Seed and outputs are mutually exclusive and one is required. State recovery from outputs needs enough consecutive samples (about 32 for glibc `random()`; fewer for plain LCGs) — too few or non-consecutive values fail with `No viable candidate found`. The author calls the project unstable: in testing, seed-based prediction worked everywhere, while output-based recovery worked for glibc but not for Java `nextInt`.

## Typical use cases

### Replay a generator from a known seed

```
foresee glibc random -s 1 -c 5
```

Outcome: prints the first five values glibc `random()` returns after `srandom(1)` — `1804289383 846930886 1681692777 1714636915 1957747793` — reproducing the target's stream without running it. Use `-c` to bound the otherwise infinite stream.

### Predict the next outputs from observed values

```
foresee glibc random -o $(cat observed.txt) -c 3
```

Outcome: reads consecutive observed outputs of the target RNG from `observed.txt`, recovers the additive-feedback state, and prints the next three values it will produce. Fed the first 40 outputs of a glibc `random()` stream it predicted outputs 41–43 exactly in testing; fewer than ~32 samples abort with an index error.

### Predict a bounded-range PHP rand()

```
foresee php linux rand -s 42 -r 1 10 -c 4
```

Outcome: prints what PHP `rand(1, 10)` returns on a Linux build for seed 42 (`1 4 7 5`); drop `-r` for the unbounded form, and pass `windows` as the platform for Windows builds. The platform argument comes before the function name.

### Replay Java java.util.Random

```
foresee java nextInt -s 42 -c 8
```

Outcome: prints the next eight `nextInt()` values for seed 42 (`-1170105035 234785527 -1360544799 205897768 ...`); `nextLong` works identically. Seed-based replay is the reliable path here — recovery from outputs only failed in testing even with consecutive values.

### Model an arbitrary LCG

```
foresee lcg 1103515245 12345 2147483648 16 -s 1 -c 5
```

Outcome: treats the generator as `state = (state * multiplier + increment) mod modulus` with outputs `state >> bitshift` — here an ANSI-C-style `rand()` — and prints the first five outputs for seed 1 (`16838 5758 10113 17515 31051`); replace `-s` with `-o values...` when only outputs are known.

*No corpus evidence — derived from upstream documentation.*
