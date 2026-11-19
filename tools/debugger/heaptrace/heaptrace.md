---
name: "heaptrace"
categories:
  - blackarch-debugger
version: "2.2.8.2.r20.g06f43fc"
homepage: "https://github.com/Arinerron/heaptrace"
packaged-by: [blackarch, aur]
blackarch-builddate: "2024-02-03"
build-system: make
license: BSD
meta-checked: "2026-11-09"
github-repo: "Arinerron/heaptrace"
github-stars: 330
github-pushed: "2023-01-27"
github-archived: false
github-issues: 18
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# heaptrace

> glibc heap operation tracer for ELF64 x86-64 binaries — runs a target and prints a symbolized, readable table of every malloc/calloc/realloc/reallocarray/free call so heap behavior, corruption and misuse patterns are visible at a glance.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Understanding what a binary actually does to the heap — exploit development against glibc-based targets (heap feng shui, tcache poisoning) and diagnosing heap corruption or leaks. It is not a general memory checker like valgrind: it visualizes the *sequence* of heap calls, replacing raw chunk addresses with stable, easy-to-follow symbols so successive allocations and frees can be correlated across the run, flags corruption and leakage issues it detects, works on stripped binaries with ASLR regardless of compiler settings, and can drop you into gdb at any heap operation. ELF64 (x86-64) binaries only.

## Typical use cases

### Trace a binary's heap activity

```
heaptrace ./target
```

Outcome: prints a live table of each malloc/calloc/realloc/reallocarray/free call — sizes, returned addresses shown as symbols, and arguments — followed by a corruption/leak summary when the target exits. Watch it scroll interactively to follow the heap layout as the program runs.

### Save the trace to a file

```
heaptrace -o trace.txt ./target
```

Outcome: writes the same table to `trace.txt` instead of stderr — useful for keeping a record or diffing heap behavior between two runs.

### Break into gdb at a chosen heap operation

```
heaptrace --break=3 ./target
```

Outcome: when the 3rd heap operation is reached the target receives SIGSTOP and gdb is attached to it, so you can inspect the exact allocator state at that call. The expression accepts more than a plain index (see the upstream wiki on breakpoints); `--break-after` instead replaces the tracer with gdb only after the heap function returns.

### Attach to a running process

```
heaptrace --attach 1234
```

Outcome: heap tracing begins for PID 1234 without restarting it — for when the interesting state is already live in a process you cannot relaunch.

### Run the target with its own environment

```
heaptrace -e LD_PRELOAD=./libc.so.6 ./target
```

Outcome: the target executes with `LD_PRELOAD=./libc.so.6` applied to it only, while heaptrace's own runtime configuration stays unaffected; repeat `-e` for each additional variable.
