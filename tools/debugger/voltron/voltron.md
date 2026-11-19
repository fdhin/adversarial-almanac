---
name: "voltron"
categories:
  - blackarch-debugger
version: "v0.1.8.r0.gbe9e54b"
homepage: "https://github.com/snare/voltron"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-17"
build-system: python
license: MIT
meta-checked: "2026-11-09"
github-repo: "snare/voltron"
github-stars: 6339
github-pushed: "2025-01-30"
github-archived: false
github-issues: 85
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# voltron

> Extensible debugger UI for GDB, LLDB, VDB and WinDbg: an RPC server inside the debugger plus views (registers, stack, disassembly, backtrace) you attach from other terminals.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Voltron makes terminal debuggers usable by splitting their display across panes: it embeds a small RPC server in your debugger (GDB, LLDB, Vivisect's VDB, or WinDbg/CDB via PyKD) and provides a `voltron` client whose views — registers, disassembly, stack, memory, breakpoints, backtrace — run in separate terminals and refresh every time the debugger stops. The point is to keep a persistent registers/stack/code layout beside your debugger CLI instead of re-typing `info registers` after every step. It complements rather than replaces the debugger prompt (and works alongside GEF/PEDA/pwndbg — disable their context display and let Voltron serve the panes), supports x86, x86_64, ARM and (per debugger) others, and runs on Linux and macOS. It does not script the debugger or add exploit-development commands itself.

## Typical use cases

### Load Voltron into GDB

```
echo "source /path/to/voltron/entry.py" >> ~/.gdbinit
gdb /path/to/target
```

Outcome: GDB starts with Voltron's server embedded (the installer normally adds this line for you); from now on any Voltron view you start can talk to this session.

### Attach live views in separate terminals

```
voltron view register
voltron view stack
voltron view disasm
voltron view backtrace
```

Outcome: each terminal becomes a live pane showing that data for the debugged process. On GDB/LLDB/WinDbg the views update immediately; on VDB they refresh when the target stops. Every step, breakpoint hit, or continue repaints all attached panes.

### Load Voltron into LLDB

```
echo "command script import /path/to/voltron/entry.py" >> ~/.lldbinit
lldb /path/to/target
```

Outcome: LLDB registers Voltron on startup; on older LLDB versions load the target first and run `voltron init` at the prompt to install the event hooks. The same `voltron view ...` clients work unchanged.

### Add Voltron to VDB

```
vdbbin /path/to/target
> script /path/to/voltron/entry.py
```

Outcome: VDB loads the Voltron server script; views attached from other terminals follow the traced process, updating whenever it stops at a breakpoint or after a step.
