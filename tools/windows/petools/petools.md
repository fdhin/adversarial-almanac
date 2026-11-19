---
name: "petools"
categories:
  - blackarch-windows
version: "1.9.762"
homepage: "https://github.com/petoolse/petools"
packaged-by: [blackarch]
blackarch-builddate: "2018-11-12"
build-system: windows
license: MIT
meta-checked: "2026-11-09"
github-repo: "petoolse/petools"
github-stars: 1203
github-pushed: "2025-10-21"
github-archived: false
github-issues: 10
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# petools

> PE Tools — the long-running Windows GUI workbench for editing, dumping, rebuilding and comparing PE files and processes (since 2002).

## What it's for

PE Tools is an oldschool reverse-engineering suite for Portable Executable files, inspired by LordPE and maintained by the petoolse team (NEOx, Jupiter, PainteR, EvilsInterrupt across its history). It bundles the pieces a manual reverser reaches for after a hex editor: a PE Editor for headers, sections, and most data directories (export/import/TLS/load-config/bound editors); a File Location Calculator converting between virtual address, RVA and raw file offset; a side-by-side PE Files Comparator; a Process Viewer that shows running processes and their modules; a PE Dumper (full, partial or region dumps of a live process); a PE Rebuilder (dump fixer, relocation wiper, resource rebuilder, imports binder, image-base changer with validation); a PE Sniffer doing signature analysis and packer detection (PEiD-style signatures ship as `Signs.txt`); and a built-in HEX editor reachable from the section and directory editors. Since v1.9 it also shows per-section entropy and includes a 64-bit disassembler view. It runs on Windows XP through 10 (administrative rights for SeDebugPrivilege when dumping processes), under Wine on other systems, and natively on ReactOS; it has no command-line interface — everything is driven from the GUI dialogs — so no scriptable examples exist. Source code is not published; the project ships release archives containing `PETools.exe`, `HEdit.dll`, `RebPE.dll` and signature files.

*No corpus evidence — derived from upstream documentation.*
