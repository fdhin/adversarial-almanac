---
name: "beebug"
categories:
  - blackarch-decompiler
version: "25.cddb375"
homepage: "https://github.com/invictus1306/beebug"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-12"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "invictus1306/beebug"
github-stars: 211
github-pushed: "2019-05-31"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# beebug

> Crash-exploitability triage for native binaries, driven by radare2.

## What it's for

Deciding whether a crash in a native program is exploitable. beebug runs a target under radare2 via r2pipe (it is radare2-based, not a GDB front-end), catches the fatal signal, and classifies the fault — stack overflow on libc, crash on the program counter, crash on a branch, invalid write, heap vulnerabilities, read access violations — then prints a verdict such as "Invalid write crash - Generally it is exploitable" together with a backtrace and a register dump. With optional DynamoRIO instrumentation (the functrace client, configured in a `config` file) it can also generate an execution/call graph of the crashing run. Known limitations: it cannot feed input to a program that waits on stdin at runtime, and the graph output only scales to small targets. Use absolute paths for targets, reports and graphs — the packaged launcher changes into its install directory before running the script.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Triage a crashing binary

Run a program that crashes and get an exploitability verdict.

```
beebug -t /home/user/lab/simple_crash -a
```

Outcome: the target starts under radare2, stops on the fault (e.g. `SIGNAL 11 errno=0 addr=0x00000000 code=1`), prints the verdict line ("Invalid write crash - Generally it is exploitable, the write value/address could be tainted - Invalid write of size 2") followed by a backtrace and full register dump.

### Combine instrumented analysis with a call graph

Reproduce the crash under DynamoRIO and capture the execution graph.

```
beebug -t /home/user/lab/simple_crash -i -r /home/user/lab/report1 -g /home/user/lab/graph1 -a
```

Outcome: the target is launched through `drrun` with the functrace client (paths set in the `config` file), the functrace report `report1` is parsed, the same exploitability verdict and register state are printed, and a call-graph PDF named `graph1` is written for viewing in any PDF reader.
