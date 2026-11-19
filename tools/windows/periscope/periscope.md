---
name: "periscope"
categories:
  - blackarch-windows
version: "3.2"
homepage: "https://vidstromlabs.com/freetools/periscope/"
packaged-by: [blackarch, aur]
blackarch-builddate: "2024-10-03"
build-system: windows
license: custom:unknown
meta-checked: "2026-11-09"
worth: 0.8
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# periscope

> Single-purpose console inspector for Windows PE files: headers, sections, imports and exports in one text dump (Vidstrom Labs).

## What it's for

PEriscope (Arne Vidstrom, Vidstrom Labs) prints a structured overview of a Portable Executable file — PE/DOS headers, sections, the import and export tables (with ordinals and RVAs), and a hash of each exported function name. It works on ordinary 32-bit PE files as well as 64-bit and .NET assemblies, which makes it a quick triage step before opening a full disassembler: spot a packed or anomalous layout, enumerate what a DLL imports and exports, compare ordinals across versions. It is deliberately minimal — it does not disassemble, edit, or unpack anything, and the author notes the import table alone can never prove what a file does. It is a console program with exactly one argument; there is no canonical GitHub repository (it is distributed from vidstromlabs.com, and same-named GitHub projects are unrelated).

## Typical use cases

### Inspect a PE file's headers, sections and tables

One argument, one dump.

```
periscope.exe sample.exe
```

Outcome: prints the PEriscope banner, then the file's PE/DOS header fields, section table, import table (DLLs, ordinals, function names) and export table (ordinals, RVAs, names, per-name hashes) to the console. If the entry point does not point into a code segment it prints an explicit warning — a classic sign of infection, though a clean report proves nothing.

### Save the inspection to a file for diffing

Capture the text dump instead of reading it on screen.

```
periscope.exe sample.exe > sample-report.txt
```

Outcome: writes the same report to `sample-report.txt` (the author documents plain `> file.txt` redirection); diff two reports to see what changed between builds of a binary, e.g. after a patch adds imports.

*No corpus evidence — derived from upstream documentation and the packaged binary's usage output.*
