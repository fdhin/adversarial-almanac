---
name: "rr"
categories:
  - blackarch-debugger
version: "6869.556551e4"
homepage: "https://github.com/mozilla/rr"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2024-07-20"
build-system: cmake
license: custom
meta-checked: "2026-11-09"
github-repo: "rr-debugger/rr"
github-stars: 10739
github-pushed: "2026-11-09"
github-archived: false
github-issues: 483
github-match: "exact"
gh-checked: "2026-11-09"
worth: 8.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 155
gh-activity-checked: "2026-11-09"
---

# rr

> Record a Linux process tree once, then replay it deterministically under GDB, with reverse execution and cheap hardware watchpoints.

*No corpus evidence — derived from upstream documentation.*

## What it's for

rr is a record-and-replay debugger: it records the execution of an application (including all its child processes and threads) to a trace on disk, then replays that trace under GDB so every run is bit-for-bit identical. The payoff for hard-to-reproduce bugs — races, memory corruption that only strikes on the Nth run, heisenbugs that vanish under a breakpoint — is that you record the failure once and then debug it forever. Inside a replay session GDB gains efficient reverse-execution commands (`reverse-continue`, `reverse-step`) and hardware data watchpoints that stop exactly when a watched address is written. It works on whole process trees, so one trace covers a parent and its children. What it does not do: it is Linux-only (kernel 4.7+), needs an Intel Nehalem-or-later, AMD Zen-or-later, or supported AArch64 CPU with virtualised performance counters, and it does not debug across machines.

## Typical use cases

### Record a failing execution once

```
rr record ./program arg1 arg2
```

Outcome: the program runs to completion under rr's scheduler; on exit rr prints the trace directory it wrote (under `~/.local/share/rr/<program>_N`). The recorded program behaves like a normal run — same output, same exit code — but the whole execution is now captured.

### Replay the last trace under GDB

```
rr replay
```

Outcome: opens GDB attached to the most recent trace with symbols for the traced process tree loaded. Every replay of this trace reproduces exactly the same registers, memory, and syscalls, so breakpoints and watchpoints hit at the same points every time. The bare form `rr /path/to/trace_dir` also replays that trace directory.

### Debug backwards from the crash

```
rr replay
(gdb) watch -l *global_ptr
(gdb) continue
(gdb) reverse-continue
```

Outcome: after hitting the crash or the point of interest, `reverse-continue` runs the trace backwards until the watched location is written or an earlier breakpoint hits — usually landing directly on the instruction that planted the bad value. `reverse-step`/`reverse-next` walk backwards one statement at a time.

### Record to a named trace directory

```
rr record -o /tmp/trace ./program
```

Outcome: writes the trace to `/tmp/trace` instead of the default location, so you can keep several traces side by side. Use `-p /tmp/trace` (`--print-trace-dir`) to print the trace directory without renaming it.

### List the processes in a trace

```
rr ps /tmp/trace
```

Outcome: prints the recorded process tree (pids, ppid, exit status, command lines) of the trace, so you can see which children were spawned and where each died before attaching in a replay session.

### Pack a trace for transport

```
rr pack /tmp/trace
```

Outcome: deduplicates files inside the trace directory and adds anything needed to replay it elsewhere, shrinking it for archiving or copying to another machine (replay there with `rr replay /tmp/trace`).
