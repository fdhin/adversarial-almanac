---
name: "daredevil"
categories:
  - blackarch-crypto
version: "42.81cb57f"
homepage: "https://github.com/SideChannelMarvels/Daredevil"
packaged-by: [blackarch]
blackarch-builddate: "2022-01-30"
build-system: make
license: GPL3
meta-checked: "2026-11-09"
github-repo: "SideChannelMarvels/Daredevil"
github-stars: 170
github-pushed: "2022-01-27"
github-archived: false
github-issues: 10
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# daredevil

> Fast multi-threaded first- and higher-order correlation power analysis (CPA) against AES and DES trace sets.

## What it's for

Daredevil (SideChannelMarvels) computes correlation power analysis attacks on side-channel trace sets, implementing the fastest approaches from Bottinelli & Bos, "Computational Aspects of Correlation Power Analysis". It is config-file driven with no interactive mode: an experiment file describes the raw trace files (32/64-bit floats or 8-bit integers, e.g. captured with the companion Deadpool/Tracer flow), the known plaintexts from which key guesses are derived, the algorithm and S-box position under attack (AES or DES, lookup tables shipped in `LUT/`), the attack order, thread count and a memory budget. It attacks every key byte in parallel or a chosen byte/bit, ranks correlations, and can report the rank of a known key. A helper script converts Riscure `.trs` captures to its native format. It requires only a compiler with OpenMP support.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Run a first-order CPA experiment over all key bytes

```
./daredevil -c experiment.conf
```

with `experiment.conf`:

```
[Traces]
files=1
trace_type=f
transpose=true
index=0
nsamples=3000
trace=traces.bin 1000000 3000

[Guesses]
guess_type=u
transpose=true
guess=plaintext.bin 1000000 16

[General]
threads=8
order=1
algorithm=AES
position=LUT/AES_AFTER_SBOX
bytenum=all
top=20
```

Outcome: loads the raw float traces and unsigned-byte plaintexts, computes correlations between power samples and AES S-box output guesses for every key byte across the chosen threads, and prints the top-correlating key candidates per byte — the highest-correlation candidate per position is the recovered key byte.

### Rank a known key during a targeted attack

```
./daredevil -c known-key.conf
```

with `known-key.conf` setting `bytenum=0`, `bitnum=all` and e.g. `correct_key=0xaa` in `[General]`.

Outcome: instead of only listing the top guesses, the tool reports the correlation rank of the specified known key byte (or whole key in hex) — used to measure how much leakage a target or countermeasure leaves at a given position.

### Run a higher-order attack on a masked implementation

```
./daredevil -c focpa.conf
```

with `focpa.conf` setting `order=2`, `window=3` and a memory cap such as `memory=4G` in `[General]`.

Outcome: computes second-order (standardized-moment) correlations over a 3-sample window across the traces within the given memory budget — the standard next step when first-order CPA shows no leakage because of masking.

### Convert Riscure TRS captures to Daredevil format

```
python trs2daredevil.py capture.trs
```

Outcome: parses the `.trs` tags and writes `capture.traces`, `capture.input` and `capture.output` plus a starter `capture.config`, ready to be passed to `daredevil -c capture.config` after tuning trace dimensions and sample counts.
