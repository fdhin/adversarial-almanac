---
name: "plasma-disasm"
categories:
  - blackarch-disassembler
version: "922.ec7df9b"
homepage: "https://github.com/joelpx/plasma"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-15"
build-system: python
license: GPL-3.0-or-later
meta-checked: "2026-11-09"
github-repo: "joelpx/plasma"
github-stars: 3069
github-pushed: "2021-08-31"
github-archived: false
github-issues: 15
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# plasma-disasm

> Interactive console disassembler for x86/ARM/MIPS that renders functions as indented, colored pseudo-code and scripts through a Python API.

## What it's for

Plasma (formerly "Reverse") is an interactive, terminal-based disassembler for x86/x64, ARM and MIPS binaries in ELF, PE or raw format. Its distinguishing feature is pseudo-decompilation: one key press re-renders a function's disassembly as indented, colorized pseudo-code that reads much closer to source than a flat instruction listing. It also ships a Python scripting API with ready-made scripts (strings, xref call graphs, crypto-constant detection, assemble/disassemble). ARM and MIPS support is partial, the project has been dormant since 2021, and its on-disk database format can still change between versions — treat it as a lightweight per-binary analysis tool rather than a long-term analysis database.

## Typical use cases

### Open a binary and read pseudo-code

Load a file and browse a function as decompiler-style output.

```
plasma -i server.bin
```

Outcome: opens the interactive console UI on server.bin; type `v main` to jump to a function, press Tab to toggle the pseudo-decompilation view, and `|` to split the window. The command help lists all shortcuts.

### Disassemble without colors

Keep output readable on terminals lacking 256-color/UTF-8 support.

```
plasma -i server.bin --nocolor
```

Outcome: same interactive session with colored syntax disabled, so listing text stays clean in plain terminals or captured logs.

### Run the bundled analysis scripts

Invoke the shipped Python scripts from inside a session.

```
plasma -i server.bin
py !strings.py
py !xrefsto.py main
py !crypto.py
```

Outcome: after the session opens, `py !strings.py` prints all strings in the binary, `py !xrefsto.py main` draws an xdot call graph of references to the function, and `py !crypto.py` flags common crypto constants. Further bundled scripts assemble code with keystone (`py !asm.py CODE`) or disassemble a hex buffer (`py !disasm.py HEX_STRING`).

*No corpus evidence — derived from upstream documentation.*
