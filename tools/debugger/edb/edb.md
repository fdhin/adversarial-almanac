---
name: "edb"
categories:
  - blackarch-debugger
version: "3337.eedd97a0"
homepage: "https://github.com/eteran/edb-debugger/"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2024-11-23"
build-system: cmake
license: GPL2
meta-checked: "2026-11-09"
github-repo: "eteran/edb-debugger"
github-stars: 2980
github-pushed: "2026-10-27"
github-archived: false
github-issues: 66
github-match: "exact"
gh-checked: "2026-11-07"
worth: 7.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 75
gh-activity-checked: "2026-11-09"
---

# edb

> Evan Teran's edb — a Qt-based graphical debugger for Linux (x86, x86-64, AArch32) inspired by OllyDbg, for interactive disassembly and reverse engineering of native binaries.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Interactive debugging and reverse engineering of native binaries when you want an OllyDbg-style GUI instead of a gdb prompt: annotated disassembly views, breakpoints, register/memory/stack inspection and a plugin API, with Capstone as the disassembly backend and optional Graphviz support. The target runs under edb's control (ptrace) while you explore it in the Qt UI. Linux is the only officially supported platform (FreeBSD/OpenBSD/macOS/Windows ports exist with varying completeness), and it is not a scriptable or headless debugger — automation belongs to gdb/lldb; edb is for interactive analysis. GPL-2, actively maintained.

## Typical use cases

### Start a binary under the debugger

```
edb --run ./target
```

Outcome: the Qt window opens with `./target` loaded as the debuggee and stopped, ready for breakpoints and stepping. Arguments given after the program name are passed to the target process.

### Attach to an already-running process

```
edb --attach 1234
```

Outcome: edb attaches to PID 1234, stops it and shows its disassembly, memory maps and threads — use this when the interesting state only exists in a live process you did not launch.

### Run the target with redirected stdin/stdout

```
edb --stdin input.txt --stdout output.txt --run ./target
```

Outcome: the debuggee reads its input from `input.txt` and writes its output to `output.txt`. The redirection options must appear before `--run`.

### Print version information

```
edb --dump-version
```

Outcome: prints a terse one-line version string and exits; `--version` prints the full version banner instead.
