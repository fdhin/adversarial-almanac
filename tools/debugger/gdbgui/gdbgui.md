---
name: "gdbgui"
categories:
  - blackarch-debugger
version: "438.773b916"
homepage: "https://github.com/cs01/gdbgui"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2026-01-12"
build-system: python
license: GPL3
meta-checked: "2026-11-09"
github-repo: "cs01/gdbgui"
github-stars: 10355
github-pushed: "2025-06-29"
github-archived: false
github-issues: 181
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# gdbgui

> Browser-based frontend to gdb — a Flask/socket.io server that turns gdb's machine interface into a full debugging UI (source view, breakpoints, registers, memory, expressions) usable from any modern browser.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Debugging anything gdb can debug (C/C++, Go, Rust, Fortran...) without leaving the browser: instead of gdb's terminal or a heavyweight IDE, `gdbgui` starts a local server, opens a browser window and gives you a source view synced to the program counter, breakpoint management, variable/registers/memory panes and an always-available raw gdb console. It speaks to a real gdb (or gdb-compatible backend such as an `rr replay` session) underneath, so it is a frontend, not a debugger itself — gdb must be installed. It is not an LLDB frontend. Documentation lives at gdbgui.com.

## Typical use cases

### Debug a binary locally

```
gdbgui ./target
```

Outcome: the server starts on http://127.0.0.1:5000 and a browser window opens with the binary preloaded; set breakpoints and run from the UI, which shows the source line, registers and disassembly as execution proceeds.

### Pass command-line arguments to the debuggee

```
gdbgui --args ./target arg1 -flag2
```

Outcome: `./target` is loaded with `arg1 -flag2` already set as its program arguments; `--args` takes everything after it literally, so it must be the last option on the command line.

### Serve the UI to a remote browser

```
gdbgui -r -p 5001
```

Outcome: gdbgui binds to all interfaces (0.0.0.0) instead of localhost and does not auto-open a browser, so the UI is reachable at `http://<server>:5001` from another machine; add `--auth-file creds.txt` (username on the first line, password on the second) to require HTTP Basic auth, and `--key`/`--cert` for HTTPS.

### Frontend an rr (record and replay) session

```
gdbgui -g 'rr replay'
```

Outcome: instead of plain gdb, gdbgui launches `rr replay` as its backend, giving the browser UI deterministic replay and reverse-debugging of a trace you recorded beforehand with `rr record ./target`.
