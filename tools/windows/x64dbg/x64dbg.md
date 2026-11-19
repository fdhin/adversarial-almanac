---
name: "x64dbg"
categories:
  - blackarch-windows
version: "2025.07.04"
homepage: "https://github.com/x64dbg/x64dbg/releases"
packaged-by: [blackarch]
blackarch-builddate: "2025-07-26"
build-system: windows
license: GPL3
meta-checked: "2026-11-09"
github-repo: "x64dbg/x64dbg"
github-stars: 50232
github-pushed: "2026-10-28"
github-archived: false
github-issues: 576
github-match: "exact"
gh-checked: "2026-11-07"
worth: 8.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 547
gh-activity-checked: "2026-11-09"
---

# x64dbg

> Open-source x64/x32 debugger for Windows — dynamic analysis of binaries with a familiar, scriptable, plugin-capable UI.

## What it's for

x64dbg is an open-source user-mode debugger for Windows PE files (EXE/DLL), shipping as two binaries: `x32dbg.exe` for 32-bit targets and `x64dbg.exe` for 64-bit ones. It is the standard free dynamic-analysis workbench in the OllyDbg ([[ollydbg]]) tradition: break on entry or anywhere, step, inspect registers/memory/stack, follow the memory map and references (including a strings view that dynamically recognizes strings in modules), patch executables, dump memory, rebuild imports with the integrated Scylla, match patterns with Yara, and automate with the built-in scripting language and plugin API. Typical uses: understanding a sample's logic while it runs (decryption loops, license checks, C2 configuration parsing), unpacking, and unpacked-dump repair. It is a GUI debugger for interactive work — no headless batch mode.

## Typical use cases

### Analyze a binary's runtime behavior

Load the sample in the matching-bitness debugger and trace the logic of interest.

```
x64dbg.exe sample.exe
```

Outcome: the binary loads and breaks at the system/entry breakpoint; from there set breakpoints (e.g. on the API calls or the address range you identified statically), run, and use stepping plus the register/memory/stack views to observe behavior — e.g. locating a loop that decrypts a buffer and reading the plaintext from memory once the loop completes. Use `x32dbg.exe sample.exe` for 32-bit samples.

### Locate a routine via references and strings

Find where interesting data is used before deciding what to breakpoint.

```
x32dbg.exe sample.exe
```

Outcome: with the sample loaded, the References/strings search lists string and call cross-references across the module; jumping to a hit shows the surrounding disassembly (with jump arrows and token highlighting), letting you pick exact addresses/imports to breakpoint on instead of guessing.

*Corpus evidence: prose reports of loading a program in x64dbg to inspect its runtime behavior (locating and understanding a decision loop) and reaching for x32dbg as the debugger of choice; features verified against the x64dbg README.*
