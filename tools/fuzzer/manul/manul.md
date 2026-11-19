---
name: "manul"
categories:
  - blackarch-fuzzer
version: "197.f525df9"
homepage: "https://github.com/mxmssh/manul"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-13"
build-system: scripts
license: Apache
meta-checked: "2026-11-09"
github-repo: "mxmssh/manul"
github-stars: 335
github-pushed: "2020-07-14"
github-archived: false
github-issues: 31
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# manul

> Coverage-guided parallel fuzzer in pure Python for open-source and black-box binaries on Windows, Linux, and macOS.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Manul (Maksim Shudrak) is a coverage-guided parallel fuzzer written in pure Python. It instruments targets in two ways — AFL-style compile-time instrumentation for builds you control, and DynamoRIO-based dynamic binary instrumentation for binaries you cannot rebuild — and also supports plain dumb fuzzing when no coverage signal exists. Targets receive input through an `@@` file placeholder, AFL-style, and multiple fuzzers run in parallel from a single command. Public CVEs have been found with it in Poppler and Bro/Zeek. It does not generate its own seeds: you provide the initial corpus directory.

## Typical use cases

### Fuzz an AFL-instrumented target in parallel

Run the standard campaign against a binary compiled with `afl-gcc`/`afl-clang-fast`.

```
manul -i corpus -o out -n 4 "./target @@"
```

Outcome: spawns 4 parallel fuzzing processes (`-n`) evolving seeds from `corpus/` into `out/`; each test case is written to the `@@` path and coverage comes from the target's AFL instrumentation. Crashes, hangs, and stats accumulate under `out/`.

### Dumb-fuzz a binary without instrumentation

Use mutation-only fuzzing when the target cannot be instrumented.

```
manul -s -i corpus -o out -n 2 "./target @@"
```

Outcome: runs the same loop with `-s` (dumb fuzzing, no coverage feedback) — progress is slower but any executable can be fuzzed as-is.

### Fuzz a black-box binary through DynamoRIO

Get coverage from a closed binary via dynamic binary instrumentation.

```
manul -c manul.config -i corpus -o out -n 2 "./target @@"
```

Outcome: reads `dbi = dynamorio` plus `dbi_root`/`dbi_client_root` (pointing at `drrun` and the `libbinafl.so` client) from the config file and gathers coverage by instrumenting the process at runtime; with `dbi_persistence_mode = 1` it re-invokes a chosen target function in a loop instead of restarting the process per test case.

### Resume a previous campaign

Continue a run after it was stopped.

```
manul -r -i corpus -o out "./target @@"
```

Outcome: restores the queues and statistics saved in the output directory and continues the campaign from where it left off.
