---
name: "dwarf"
categories:
  - blackarch-binary
version: "1084.fd859ae"
homepage: "https://github.com/iGio90/Dwarf"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "iGio90/Dwarf"
github-stars: 1320
github-pushed: "2024-05-16"
github-archived: false
github-issues: 5
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# dwarf

> GUI reverse-engineering debugger built on PyQt5 and frida, attaching to local, Android and iOS processes.

## What it's for

Dwarf is a debugger front end for dynamic analysis: it drives frida under
the hood and adds a full desktop UI — a program tree over the target's
modules, namespaces and symbols, native plus Java breakpoints, live memory
and hex views, hook management and an interactive JavaScript console against
the frida/Dwarf API. It spawns or attaches to local binaries, Android apps
(through frida-server) and iOS processes, so the same workflow covers
desktop software and mobile apps. Helper commands round it out:
`dwarf-creator` scaffolds an agent project with API typings, and
`dwarf-trace` runs scripted traces headlessly. It is a runtime tool — pair
it with a static disassembler rather than replacing one.

## Typical use cases

### Start the GUI and pick a target interactively

```
dwarf
```

Outcome: opens the PyQt5 attach wizard to choose a session type
(local/Android/iOS/remote) and target; once attached, the program tree and
debugger panels populate with the process's modules and symbol namespaces.

### Debug a local binary from process start

```
dwarf -t local /usr/bin/target
```

Outcome: Dwarf spawns the binary under frida and attaches the UI to it,
ready for breakpoints on native functions or addresses; the program tree
shows its libraries and symbol namespaces (e.g. Rust or C++ crate/module
namespaces) as you step through execution.

### Attach to an Android app by package or PID

```
dwarf -t android com.example.app
```

Outcome: with frida-server reachable on the device, Dwarf spawns the app
and enables both native and Java instrumentation; the PID form
`dwarf -t android 1234` attaches to an already-running process instead.

### Load a custom frida agent alongside the UI

```
dwarf -t local -s /path/to/agent.js /usr/bin/target
```

Outcome: the supplied JavaScript agent is loaded into the session together
with the GUI, so hooks, memory scans or RPC scripted against the Dwarf API
run while the UI keeps its live views on the target.

### Trace a Java API without the GUI

```
dwarf-trace -t android --java java.io.File.$init com.example.app
```

Outcome: spawns the app headlessly, attaches, and prints every call of the
traced constructor with its arguments (each path passed to `java.io.File`),
then detaches — a quick data-flow probe with no manual clicking.

### Trace a native function with register values

```
dwarf-trace -t android --native --native-registers x0,x1,sp open+0x32
```

Outcome: prints each hit of the target — symbol+offset, `module@address`,
an absolute address, or a bare symbol like `popen` — together with the
selected registers at that point, tracing native activity headlessly.

*Corpus evidence: 5 mentions across 1 machine(s) — GUI sessions (program
tree, namespaces and disassembly panels) driving dynamic analysis of a
stripped native binary.*
