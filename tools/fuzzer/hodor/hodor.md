---
name: "hodor"
categories:
  - blackarch-fuzzer
version: "1.01be107"
homepage: "https://github.com/nccgroup/hodor"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "nccgroup/hodor"
github-stars: 129
github-pushed: "2016-08-31"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# hodor

> Config-file-driven file fuzzer that mutates delimited regions, byte ranges or whole inputs — somewhere between a dumb and a smart fuzzer.

## What it's for

Hodor (NCC Group) mutates known-good input instead of generating from scratch: only the text between configurable delimiters, only the byte ranges listed for binary files, or the entire file. The mutation methods (Miller-style byte mutation, TotesRand random data, BFlipper bit flipping, Quid Pro Quo token swapping) and the output sinks — stdout, a directory on disk, a TCP or SSL network target, or a spawned process whose crashes are logged to `crashlog.txt` — are all configured in the bundled `config_hodor.py`; edit that file before running. The command line only selects the input mode (`-t` text, `-b` binary, `-f` full mutation, `-` stdin). It is a Python 2.7 tool and has not been developed since 2016.

## Typical use cases

### Mutate delimited text regions

Fuzz only the parts of a seed that sit between the configured delimiters.

```
hodor -t seed.txt
```

Outcome: mutates every region of `seed.txt` enclosed by the config's `text_delimiter` (e.g. `@@...@@`) and sends each mutation to the configured sink (stdout by default).

### Fuzz an entire file

Mutate everything, sparing nothing.

```
hodor -t seed.txt -f
```

Outcome: every byte of `seed.txt` is eligible for mutation, one mutation per iteration written to the configured sink.

### Fuzz piped input

Use another program's output as the fuzzing seed.

```
printf 'A%.0s' {1..50} | hodor -t -f
```

Outcome: reads the 50 A's from stdin as the known-good input and emits full mutations of it — useful for generating structured seeds on the fly.

### Mutate binary byte ranges

Fuzz specific offsets of a binary format and keep the rest intact.

```
hodor -b sample.png > mutated.png
```

Outcome: mutates only the byte ranges listed in the config's `bin_fields` (Python-style `(start, end]` intervals) and writes the mutated file to stdout — redirect it, then diff against the original to locate the changed bytes.

*No corpus evidence — derived from upstream documentation.*
