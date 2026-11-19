---
name: "snowman"
categories:
  - blackarch-windows
version: "0.1.3"
homepage: "http://derevenets.com/"
packaged-by: [blackarch, aur]
blackarch-builddate: "2020-06-15"
build-system: windows
license: GPL2
meta-checked: "2026-11-09"
github-repo: "yegord/snowman"
github-stars: 2242
github-pushed: "2023-03-09"
github-archived: true
github-issues: 94
github-match: "exact"
gh-checked: "2026-11-09"
worth: 0.8
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# snowman

> Snowman — a native x86/x86-64/ARM decompiler that turns binaries into C/C++-like pseudocode, with a Qt GUI.

## What it's for

Snowman is a free decompiler by Yegor Derevenets that disassembles native executables (x86, AMD64 and ARM) and reconstructs C/C++-like source from them — an open-source answer to Hex-Rays for quickly reviewing what a binary computes without reading assembly. It loads PE and ELF files, presents a function tree alongside the decompiled code, and follows calls from the entry point. The project grew out of the academic SmartDec decompiler (the source lineage continues in the smartdec/smartdec repository, where the tools are `nocode`, the GUI and an IDA plug-in); the Snowman GitHub repository is archived, and 0.1.3 is the final release — the version packaged here, as a Windows x64 build from the author's site. For sustained interactive work it has been overtaken by Ghidra, but it remains a lightweight way to obtain pseudocode for 32- and 64-bit binaries. Licensed under the GPLv3.

## Typical use cases

### Open a binary straight into the decompiler

```
snowman.exe sample.exe
```

Outcome: the Qt GUI starts with the file already loaded (file names given on the command line are opened at startup); the functions view fills from the entry-point analysis, and selecting a function shows its reconstructed C/C++-like pseudocode next to the disassembly.

*No corpus evidence — derived from upstream documentation.*
