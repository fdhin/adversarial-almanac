---
name: "hopper"
categories:
  - blackarch-reversing
version: "5.19.7"
homepage: "https://www.hopperapp.com/"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2026-02-14"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
worth: 0.8
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# hopper

> Interactive disassembler, decompiler and debugger for macOS, Linux and Windows binaries, by Vincent Bénony.

## What it's for

Hopper is a native-code reverse-engineering workbench: load an executable (x86/x64, ARM and friends across Mach-O, ELF and PE) and it presents navigable disassembly with pseudo-code side by side, lets you annotate and modify the database, apply patches, and (on supported targets) debug. It is the classic lighter-GUI alternative to IDA when analyzing macOS and Linux binaries, and it is commercial software — a paid license with a time-unlimited but feature-limited demo (the packaged Linux build is the vendor's demo tarball), so expect export/save restrictions until licensed. Workflows are interactive: open the binary, wait for auto-analysis, then read decompiled methods, rename, and patch from the UI. There is no documented command-line or headless surface — the packaged `hopper` executable is simply the GUI launcher — so automation beyond opening a file from the shell is not part of the product.

*No command-line surface is documented upstream; this note is description-only. Grounding: upstream documentation and the packaged vendor demo; corpus usage is GUI-workflow prose (reversing a shared object and a kernel driver in a trial copy) with no command lines.*
