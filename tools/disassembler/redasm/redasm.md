---
name: "redasm"
categories:
  - blackarch-disassembler
version: "1667.5ab6be9"
homepage: "https://github.com/REDasmOrg/REDasm"
packaged-by: [blackarch]
blackarch-builddate: "2021-01-24"
build-system: cmake
license: GPL3
meta-checked: "2026-11-09"
github-repo: "REDasmOrg/REDasm"
github-status: "gone"
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
---

# redasm

> Qt5-based interactive multi-architecture disassembler (PE, ELF, DEX, PS1, XBE, GBA/N64 ROMs) with Capstone-backed processors and a signature database.

## What it's for

REDasm is a GUI disassembler written in C++ with a Qt5 front end, aimed at everyone from hobbyists to professional reverse engineers. It loads many exotic formats — PE (32/64-bit), ELF (32/64-bit, both endians), Android Dalvik (DEX), Sony Playstation 1 executables (with PsyQ 4.6/7 signatures available), original-Xbox XBE, and in-development Game Boy Advance and Nintendo 64 ROMs — and disassembles x86/x86-64, MIPS and 32-bit ARM via Capstone. The core is modular: file formats and instruction sets are plugins, and later versions of the project added Python 3 plugins, multithreaded analysis, and an intermediate language (RDIL). Note the project is dead — the GitHub repository has been deleted and the redasm.io website is stopped, and the packaged build dates from the 2021-era source — and it is a point-and-click GUI application, not a batch or command-line analyzer.

## Typical use cases

### Launch the GUI disassembler

Start an interactive analysis session.

```
redasm
```

Outcome: opens the Qt5 REDasm window (the packaged wrapper runs it from /usr/share/redasm together with its bundled LibREDasm library and signature database). Open a binary via the file dialog; the matching format loader is chosen automatically and the disassembly listing appears for interactive annotation.

*No corpus evidence — derived from upstream documentation.*
