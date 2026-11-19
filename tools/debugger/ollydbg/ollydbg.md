---
name: "ollydbg"
categories:
  - blackarch-debugger
version: "201g"
homepage: "http://www.ollydbg.de"
packaged-by: [blackarch, kali]
blackarch-builddate: "2018-12-14"
build-system: scripts
license: custom
meta-checked: "2026-11-09"
worth: 0.8
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# ollydbg

> The classic 32-bit assembler-level analysing debugger for Windows by Oleh Yuschuk (ollydbg.de) — the reference tool for a generation of PE reverse engineering, extensible through a large plugin ecosystem.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Interactively reverse-engineering 32-bit Windows executables: load a PE and OllyDbg's heuristic analysis annotates the disassembly with labels, arguments and loops; from there you set (conditional) breakpoints including on API calls, step and trace runs, inspect and search memory and the stack, follow calls, and patch bytes in place or dump a modified process to disk. It is a GUI-only Windows application (shareware, current version 2.01) distributed from the author's site — there is no canonical source repository. It dominated Windows reversing through the 2000s and remains useful for 32-bit binaries and old malware; 64-bit targets need a different tool (x64dbg deliberately clones its look and workflow). On Linux distributions the packaged `ollydbg` command is a wrapper that sets up a Wine profile (`~/.ollydbg`) and launches the Windows executable under Wine.

## Typical use cases

### Launch OllyDbg under Wine

```
ollydbg target.exe
```

Outcome: on first run the wrapper creates `~/.ollydbg` with symlinks to the installed `ollydbg.exe` and `dbghelp.dll`, then starts the OllyDbg GUI via Wine; `target.exe` and any further arguments are handed to the debugger. All actual work — opening the module, breakpoints, tracing, patching — happens in the GUI (File → Open also loads a module if it was not passed on the command line).
