---
name: "ghidriff"
categories:
  - blackarch-reversing
version: "v1.0.0.r6.g4f5d895"
homepage: "https://github.com/clearbluejar/ghidriff"
packaged-by: [blackarch]
blackarch-builddate: "2026-05-31"
build-system: python
license: GPL-3.0-or-later
meta-checked: "2026-11-09"
github-repo: "clearbluejar/ghidriff"
github-stars: 819
github-pushed: "2026-10-26"
github-archived: false
github-issues: 39
github-match: "exact"
gh-checked: "2026-11-07"
worth: 5.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 4
gh-activity-checked: "2026-11-09"
---

# ghidriff

> Command-line binary diffing engine built on Ghidra, aimed at one-command patch diffing.

## What it's for

Answering "what changed between these two builds?" without opening a GUI. `ghidriff` drives Ghidra's ProgramAPI (via pyghidra/jpype) to analyze two arbitrary binaries and reports their added, deleted and modified functions — the classic patch-diff workflow of comparing a security-patched build against the vulnerable one, reduced to a single command. Results are stored as JSON and rendered as markdown (optionally side-by-side HTML), formatted so a diff can be dropped into a gist or report. It needs a Ghidra installation available at run time and is happiest on binaries that analyze cleanly in Ghidra; its speed claim is a full Windows-kernel diff in under a minute once analysis is cached.

## Typical use cases

### Diff two builds of the same binary

```
ghidriff fw-v1.bin fw-v2.bin
```

Outcome: both files are analyzed into a Ghidra project, then a function-level diff (added/modified/deleted, with decompiled bodies for changed functions) is written under `.ghidriffs/` as `<old>-<new>_diff.md` plus a machine-readable `_diff.json`.

### Pick a different diffing engine

```
ghidriff --engine VersionTrackingDiff old.dll new.dll
```

Outcome: the diff runs with Ghidra's version-tracking engine instead of the default structural diff, trading speed for better function matching when the two builds have drifted further apart.

### Diff raw firmware at a fixed load address

```
ghidriff --base-address 0x80000 fw-a.bin fw-b.bin
```

Outcome: position-independent firmware images are loaded at the given base address so code references resolve, making the function-level diff meaningful for bare-metal images.

### Tune resources for very large binaries

```
ghidriff --jvm-args=-Xmx8G --decompiler-timeout 120 a.exe b.exe
```

Outcome: Ghidra's JVM gets an 8 GB heap and each decompilation a 120-second timeout, keeping large diff jobs (whole kernels, big DLLs) from failing mid-run.

*No corpus evidence — derived from upstream documentation.*
