---
name: "honggfuzz"
categories:
  - blackarch-fuzzer
version: "4182.940b958d"
homepage: "https://github.com/google/honggfuzz"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2026-09-28"
build-system: make
license: Apache
meta-checked: "2026-11-09"
github-repo: "google/honggfuzz"
github-stars: 3409
github-pushed: "2026-09-25"
github-archived: false
github-issues: 31
github-match: "search"
gh-checked: "2026-11-07"
worth: 7.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 39
gh-activity-checked: "2026-11-09"
---

# honggfuzz

> Google's security-oriented, feedback-driven evolutionary fuzzer with persistent mode and hardware-based coverage.

## What it's for

Honggfuzz is a general-purpose fuzzer that evolves inputs using code coverage: software instrumentation by default, or hardware feedback (Intel BTS/PT performance counters) on supported CPUs. It monitors targets through ptrace to catch hijacked signals and hidden crashes, supports black-box fuzzing of uninstrumented binaries, and offers a persistent in-process mode with very high iteration rates. Targets are instrumented by compiling with the `hfuzz-clang`/`hfuzz-clang++` wrappers created when honggfuzz is built; an empty corpus directory is enough to start, since the fuzzer builds a valid input set automatically. Its trophy list includes OpenSSL, Apache, OpenSSH, Samba and FreeType vulnerabilities, and it is used by OSS-Fuzz, Android and Bitcoin Core.

## Typical use cases

### Fuzz an uninstrumented binary (black-box)

Fuzz any existing executable without recompiling it.

```
honggfuzz -i corpus/ -x -- /usr/bin/djpeg ___FILE___
```

Outcome: runs the target repeatedly with mutated corpus files substituted where `___FILE___` appears in the command line; `-x` disables instrumentation feedback, and unique crashes are saved to the crash directory (`--crashdir`, default the workspace).

### Feed input on stdin

Fuzz a program that reads standard input instead of a file.

```
honggfuzz -i corpus/ -x -s -- ./parser
```

Outcome: each mutated input is piped to `./parser` on FD 0 rather than written to a file and passed as an argument.

### Fuzz an instrumented target in persistent mode

Compile with the wrapper, then fuzz in-process for maximum throughput.

```
hfuzz-clang -o target target.c
honggfuzz -i corpus/ -P -- ./target
```

Outcome: the instrumented target is kept alive and fed inputs in-process (persistent mode is auto-detected), with coverage feedback steering mutations toward new code paths; tune concurrency with `-n <threads>` and per-run timeout with `-t <seconds>`.

### Minimize a corpus

Shrink an input corpus to the files that actually contribute coverage.

```
honggfuzz -i corpus/ --output minimized/ -P -M -- ./target
```

Outcome: `-M` minimizes the corpus, writing the retained inputs to `minimized/` — always set `--output` to a separate directory, otherwise files may be deleted from the input corpus itself.

*No corpus evidence — derived from upstream documentation.*
