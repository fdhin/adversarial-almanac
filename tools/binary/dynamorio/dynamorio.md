---
name: "dynamorio"
categories:
  - blackarch-binary
version: "9.0.19046"
homepage: "https://github.com/DynamoRIO/dynamorio"
packaged-by: [blackarch, aur]
blackarch-builddate: "2022-02-27"
build-system: scripts
license: BSD
meta-checked: "2026-11-09"
github-repo: "DynamoRIO/dynamorio"
github-stars: 3200
github-pushed: "2026-11-07"
github-archived: false
github-issues: 1832
github-match: "exact"
gh-checked: "2026-11-07"
worth: 8.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 311
gh-activity-checked: "2026-11-09"
---

# dynamorio

> Dynamic binary instrumentation framework: runs unmodified binaries under client code that observes and rewrites every instruction.

## What it's for

DynamoRIO is a runtime code manipulation system: it takes over execution of
an unmodified program and lets a "client" — a shared library written against
its API — inspect, log or transform every instruction as it executes, on
IA-32, AMD64, ARM and AArch64 under Linux, Windows and Android. It is a
mainstream, legitimate instrumentation platform: Dr. Memory (memory
debugging), drcachesim (cache simulation), drcpusim, drstrace and WinAFL's
coverage engine are all built on it. The same transparency makes it a strong
reverse-engineering aid — no source, symbols or debugger required — because
a client can record executed basic blocks, memory accesses or library-call
activity of an unfamiliar binary with modest overhead. It observes rather
than attacks: use it to understand behavior, not as an exploit primitive.

## Typical use cases

### Run a target under a custom instrumentation client

```
drrun -c libmyclient.so -- /path/to/target arg1
```

Outcome: drrun configures DynamoRIO for the target in one step and launches
it under the given client library; everything after the `--` goes to the
target. Run `drrun` with no arguments to list all supported options and
environment shortcuts.

### Collect code coverage of a run

```
drrun -t drcov -- /path/to/target
```

Outcome: the bundled drcov client records every basic block executed and
writes a per-process log such as `drcov.target.1234.0000.proc.log` into the
current directory; `-dump_text` switches the log to text format and
`-logdir` redirects it.

### Convert drcov logs to lcov for reporting

```
drcov2lcov -dir . -output coverage.info -src_filter src/
```

Outcome: all `drcov.*.log` files in the directory are merged into a single
lcov-format `coverage.info` (the default output name); `-src_filter`
restricts the report to files under your source root so system libraries
are excluded, and lcov's genhtml can then render per-line HTML coverage.

### Attach to an already-running process

```
drrun -attach 1234 -c libmyclient.so
```

Outcome: DynamoRIO attaches to PID 1234 and injects the client — an
experimental feature, but handy for long-lived services where restarting
under drrun is disruptive.

### Trace memory behavior with the bundled cache simulator

```
drrun -t drcachesim -- /path/to/target
```

Outcome: instruction and data references are traced and analyzed, with
cache and TLB statistics printed when the target exits — an example of a
full analysis tool packaged for drrun's `-t` launcher (newer releases
rename the framework to drmemtrace).

### Follow a whole process tree

```
drrun -- /bin/bash myscript.sh
```

Outcome: the script runs with DynamoRIO following into every child process
it spawns, so clients observe the complete process tree; use
`-no_follow_children` to restrict instrumentation to the initial process.

*No corpus evidence — derived from upstream documentation.*
