---
name: "fuzzdiff"
categories:
  - blackarch-fuzzer
version: "1.1"
homepage: "http://vsecurity.com/resources/tool/"
packaged-by: [blackarch]
blackarch-builddate: "2025-12-14"
build-system: scripts
license: GPL-3.0-or-later
meta-checked: "2026-11-09"
github-repo: "OblivionDev/fuzzdiff"
github-stars: 1
github-pushed: "2015-05-15"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# fuzzdiff

> Crash-input minimizer by Dan Rosenberg (Virtual Security Research) that "un-fuzzes" a crashing file down to the bytes that matter.

## What it's for

fuzzdiff is the cleanup step after a fuzzing campaign: given an original seed file, a fuzzed variant that is known to crash a target, and the target command, it repeatedly reverts random fuzzed bytes back to their original values, re-launches the target on the result, and keeps every reversion that still crashes (detected via SIGSEGV). What survives is a crashing input containing a near-minimal set of changes from the original — far easier to diff and analyze than a heavily mutated file. It is a probabilistic heuristic, not a fuzzer itself. Constraints: Unix-only signal semantics, original and fuzzed files must be the same size, and the target program must accept the input file path as its last argument. Tunables (`THRESHOLD` revert probability, `ITER` iterations, `WAIT` seconds) are constants at the top of the script.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Minimize a crashing input

Reduce a fuzzed crasher to the smallest set of changed bytes that still triggers the bug.

```
fuzzdiff orig.bin fuzzed.bin ./crash_target
```

Outcome: fuzzdiff iteratively reverts fuzzed bytes and re-runs the target, printing progress lines like `[*] Reduced diffs from 41 to 17` until no further reduction is found, then writes the minimized crashing file to `fuzz.out`. Confirm with `./crash_target fuzz.out` that it still segfaults.

### Minimize through a wrapper command with arguments

Pass program arguments before the automatically appended file path.

```
fuzzdiff orig.png fuzzed.png identify -verbose
```

Outcome: each iteration executes `identify -verbose /tmp/fuzz123` (the working copy fuzzdiff substitutes for the file argument); the final `fuzz.out` is a same-size PNG that crashes the command with a minimum of mutated bytes relative to `orig.png`.
