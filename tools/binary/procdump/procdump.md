---
name: "procdump"
categories:
  - blackarch-binary
version: "3.5.1.r0.g5c01514"
homepage: "https://github.com/Microsoft/ProcDump-for-Linux"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2026-05-09"
build-system: cmake
license: MIT
meta-checked: "2026-11-09"
github-repo: "Microsoft/ProcDump-for-Linux"
github-stars: 3090
github-pushed: "2026-11-06"
github-archived: false
github-issues: 22
github-match: "exact"
gh-checked: "2026-11-07"
worth: 7.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 20
gh-activity-checked: "2026-11-09"
---

# procdump

> Microsoft's ProcDump reimagined for Linux (and macOS): captures core dumps of a process when performance triggers fire — CPU, memory, .NET GC, thread count, signals, exceptions.

## What it's for

Use this to capture a core dump of a misbehaving process at exactly the moment a threshold is crossed (CPU spike, memory growth, .NET GC/exception, thread explosion), instead of guessing when to run `gcore` manually. Since release 1.3 the command-line switches are aligned with the Windows Sysinternals ProcDump, so skills transfer between platforms; the Linux version additionally supports memory-leak resource tracking (`-restrack`) and eBPF-backed monitoring. Dumps land in the current directory unless a dump file or folder is given, and `-w` lets you wait for a process that has not started yet. It is a monitoring/dump tool — it does not analyze the dumps; load those in gdb, lldb, or dotnet-dump afterward. This note covers the Linux project; the Windows Sysinternals utility of the same name is a separate product with its own download.

## Typical use cases

### Dump a process immediately

Take a core dump of a running process right now.

```
sudo procdump 1234
```

Outcome: writes a core dump of PID 1234 to the current directory without waiting for any trigger.

### Dump on a CPU threshold

Capture dumps only while the process is hot.

```
sudo procdump -c 65 -n 3 1234
```

Outcome: writes up to 3 core dumps, one each time CPU usage reaches 65% or more (at least 10 seconds apart), then exits — ideal for catching intermittent CPU spikes.

### Dump on memory thresholds

Capture when commit crosses set levels.

```
sudo procdump -m 100,200 1234
```

Outcome: writes one core dump when memory usage reaches 100 MB and another when it reaches 200 MB; `-cl`/`-ml` invert the comparison for below-threshold triggers.

### Dump when CPU leaves a healthy range

Trigger only outside a band.

```
sudo procdump -cl 10 -c 65 1234
```

Outcome: writes a core dump whenever CPU usage is below 10% or above 65%, catching both idle-spin and overload states.

### Track a memory leak without dumping

Report unfreed allocations instead of (or alongside) dumps.

```
sudo procdump -m 100 -restrack nodump 1234
```

Outcome: when memory crosses 100 MB, writes a `.restrack` report listing malloc-family allocations that were never freed (with call stacks) and skips core-dump generation; drop `nodump` to get both.

### Wait for a process to start

Attach to something that has not launched yet.

```
sudo procdump -w myservice -n 1
```

Outcome: procdump waits for a process named `myservice` to appear and captures one core dump of it as soon as it matches, useful for startup-time crashes.

*No corpus evidence — derived from upstream documentation.*
