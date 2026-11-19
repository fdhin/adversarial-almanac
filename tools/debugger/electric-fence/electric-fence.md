---
name: "electric-fence"
categories:
  - blackarch-debugger
version: "2.2.5"
homepage: "https://packages.debian.org/sid/electric-fence"
packaged-by: [blackarch]
blackarch-builddate: "2020-02-25"
build-system: make
license: GPL2
meta-checked: "2026-11-09"
github-repo: "SkyBME/electric-fence"
github-stars: 0
github-pushed: "2014-05-08"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# electric-fence

> Bruce Perens' Electric Fence — a malloc(3) debugger library that places an inaccessible guard page after (or before) every heap allocation, so the first out-of-bounds read or write segfaults exactly at the offending instruction.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Finding heap overruns and use-after-free in C/C++ programs without recompiling or byte-level tracking: Electric Fence is a drop-in malloc replacement (libefence, documented in libefence(3)) loaded with `LD_PRELOAD` that uses the virtual-memory hardware — every allocation ends at a page boundary followed by a protected page, and freed memory is protected the same way. Because detection is done by the MMU, the program stops on the precise faulting instruction, and reads are caught as well as writes; open it in any debugger to see the guilty statement. It does not produce a symbolic report (no leak inventory like valgrind) — you get a SIGSEGV and investigate. Every allocation costs at least a page of address space and the target runs much slower, so it is a lab instrument for targeted runs. The packaged `electric-fence` command is the classic `ef` wrapper script: it simply runs a command with `LD_PRELOAD=libefence.so.0.0`. Further switches (see the man page): `EF_ALLOW_MALLOC_0=1` stops trapping malloc(0), `EF_ALIGNMENT` controls detection granularity, `EF_FILL=<0-255>` pre-fills allocations to expose reads of uninitialized memory, `EF_DISABLE_BANNER=1` silences the banner.

## Typical use cases

### Run a program with heap bounds checking

```
electric-fence ./target
```

Outcome: the Electric Fence banner is printed, then the target runs; on the first overrun past (or, with `EF_PROTECT_BELOW`, before) any heap allocation it dies with SIGSEGV at the exact faulting instruction. Running the wrapper with no arguments prints its usage line.

### Catch the faulting instruction under gdb

```
gdb --args electric-fence ./target
```

Outcome: gdb starts the target through the wrapper (the LD_PRELOAD exec preserves the inferior); type `run` and when a guard page is hit gdb stops on SIGSEGV in the offending statement, with `bt` showing the call path. This is the documented mode — EF can produce huge core files, so debugging live beats post-mortem.

### Detect buffer under-runs instead of over-runs

```
EF_PROTECT_BELOW=1 electric-fence ./target
```

Outcome: the inaccessible page is placed *before* each allocation, so negative-index writes that underrun a buffer fault instead of overruns; alignment is ignored and every allocation is page-aligned in this mode.

### Detect accesses to freed memory

```
EF_PROTECT_FREE=1 electric-fence ./target
```

Outcome: freed memory is never recycled and stays protected, so any use-after-free access faults immediately. Memory consumption can grow dramatically while this is set — that is the price of never reusing freed chunks.
