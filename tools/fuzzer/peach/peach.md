---
name: "peach"
categories:
  - blackarch-fuzzer
version: "3.0.202"
homepage: "https://peachtech.gitlab.io/peach-fuzzer-community/"
packaged-by: [blackarch]
blackarch-builddate: "2015-01-18"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# peach

> Smart fuzzing platform (Peach 3) that runs generation- and mutation-based campaigns from XML pit files, with debugger-based crash monitoring.

## What it's for

Peach is the Peach Fuzzing Platform by Michael Eddington / Peach Tech — a smart fuzzer capable of both generation and mutation fuzzing. You describe the target protocol or file format in an XML pit file (data model, state model, agents and monitors such as debuggers), and the Peach runtime drives test cases from it. Version 3 is a .NET application run under Mono on Linux (the packaged launcher is `mono peach.exe`). The community edition is MIT-licensed; the commercial line was acquired by GitLab, and current community documentation lives at peachtech.gitlab.io/peach-fuzzer-community. Do not confuse it with [[peach-fuzz]], an unrelated Python file scanner sharing the name.

## Typical use cases

### Validate a pit file before fuzzing

```
peach -t HTTP.xml
```

Outcome: parses and validates the pit file without sending anything, printing any model or configuration errors — the recommended first step whenever a pit is written or edited.

### Run a bounded, reproducible campaign

```
peach --seed 12345 --range 0,100 HTTP.xml
```

Outcome: executes test cases 0 through 100 under a fixed RNG seed so runs can be reproduced, logging each iteration to a timestamped logs directory; faults are captured with the offending test case for replay.

### Count deterministic test cases

```
peach -c HTTP.xml
```

Outcome: reports how many test cases the pit's deterministic strategies will generate, useful for sizing a campaign before committing time to it.

### Split a campaign across machines

```
peach -p 4,2 --skipto 1000 HTTP.xml
```

Outcome: runs this pit as machine 2 of 4, taking a distinct slice of the test-case space after skipping the first 1000 cases, letting several hosts fuzz the same pit without overlap.

### Re-run a single crashing test case

```
peach -1 --seed 12345 HTTP.xml
```

Outcome: performs exactly one iteration under the configured monitors (for example a debugger agent) — the standard way to re-test the test case number recorded in a fault log.

*No corpus evidence — derived from upstream documentation.*
