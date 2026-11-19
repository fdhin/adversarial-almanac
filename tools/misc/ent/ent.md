---
name: "ent"
categories:
  - blackarch-misc
version: "1.0"
homepage: "http://www.fourmilab.ch/random"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2017-07-10"
build-system: make
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "Fourmilab/ent_random_sequence_tester"
github-match: "exact"
github-stars: 124
github-pushed: "2024-01-25"
github-archived: false
github-issues: 5
gh-checked: "2026-11-09"
worth: 3.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# ent

> Statistical tester for random data: entropy, chi-square, arithmetic mean, Monte Carlo Pi, and serial correlation.

*No corpus evidence — derived from upstream documentation.*

## What it's for

ent (by John Walker, Fourmilab) applies a battery of statistical tests to the stream of bytes (or bits) in a file and reports the results: information density (entropy) in bits per character, a chi-square test against the uniform distribution, the arithmetic mean of the data, a Monte Carlo estimate of Pi, and the serial correlation coefficient. Reach for it when evaluating pseudorandom number generators for encryption or statistical sampling, estimating how much a file can compress, or checking whether supposedly random data (keystreams, keys, encoded payloads) really is random. It only measures existing streams — it does not generate data — and reads from a file argument or standard input. It is distributed from the author's site (fourmilab.ch/random), which also links its GitHub home, Fourmilab/ent_random_sequence_tester.

## Typical use cases

### Run the full battery on a sample

Judge whether a generator or file is close to random.

```
head -c 100000 /dev/urandom > sample.bin && ent sample.bin
```

Outcome: prints entropy in bits per byte, the chi-square value with the percentage of times a random sequence would exceed it, the arithmetic mean (127.5 = random for bytes), the Monte Carlo Pi estimate, and the serial correlation coefficient (0.0 = uncorrelated). A chi-square percentage above 99% or below 1% means the data is almost certainly not random.

### Produce machine-readable statistics

Feed the metrics to a spreadsheet or script.

```
ent -t sample.bin > stats.csv
```

Outcome: writes terse CSV records (a header line naming the fields, then one line of values: file length, entropy, chi-square, mean, Pi, correlation) instead of the human-readable report — suitable for logging many files over time.

### Show the byte-value distribution

See which byte values dominate the file.

```
ent -c sample.bin
```

Outcome: appends a table of every occurring byte value (printable Latin-1 characters shown alongside their decimal codes) with its occurrence count and fraction of the file — skewed distributions explain a suspicious chi-square result.

### Analyze a bitstream instead of bytes

Test randomness at the single-bit level.

```
ent -b -t keystream.bin
```

Outcome: statistics are computed over the input treated as a stream of individual bits (mean target becomes 0.5); useful for testing the low-order output of a generator, where byte-level tests can hide bit-level bias.
