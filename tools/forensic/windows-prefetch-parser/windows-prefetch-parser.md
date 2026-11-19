---
name: "windows-prefetch-parser"
categories:
  - blackarch-forensic
version: "88.bc1fa58"
homepage: "https://github.com/PoorBillionaire/Windows-Prefetch-Parser"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-14"
build-system: python
license: Apache
meta-checked: "2026-11-09"
github-repo: "PoorBillionaire/Windows-Prefetch-Parser"
github-stars: 123
github-pushed: "2024-05-29"
github-archived: false
github-issues: 8
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---
# windows-prefetch-parser

> Parses Windows Prefetch (.pf) files from XP through Windows 10 into run counts, last-run times and loaded-module lists.

## What it's for

Windows' application prefetcher records, per executable, how often and when it last ran plus the files and directories it touched — one of the standard artifacts for evidence of program execution. `prefetch.py` (the Windows-Prefetch-Parser project by PoorBillionaire, Apache license) reads all prefetch format versions 17, 23, 26 and 30, i.e. XP through Windows 10, and handles the compression used in Windows 10 prefetch files. It accepts a single file or a whole directory and prints a text report or CSV. It is a focused .pf reader: no timeline correlation, no shim-cache or AmCache parsing.

## Typical use cases

### Report on a single prefetch file

Answer "did this program run on this system, and when".

```
prefetch.py -f CMD.EXE-4A81B364.pf
```

Outcome: prints the executable name, run count, last-executed timestamp, volume information (device path, creation date, serial number) and the lists of directory strings and loaded resources (DLLs and data files).

### Batch-process a Prefetch folder

Sweep an acquired system's whole prefetch set.

```
prefetch.py -f /mnt/c/Windows/Prefetch/
```

Outcome: parses every .pf file in the directory in turn and emits the same per-file report for each — the standard sweep over `C:\Windows\Prefetch` from a mounted evidence image.

### Emit CSV for a timeline

Get output suitable for sorting or importing.

```
prefetch.py -c -f /mnt/c/Windows/Prefetch/ > prefetch.csv
```

Outcome: one CSV line per prefetch file (last executed, executable name, run count) that can be sorted by time or merged into a super-timeline.

*No corpus evidence — derived from upstream documentation.*
