---
name: "rcrdcarver"
categories:
  - blackarch-forensic
version: "5.54507d21"
homepage: "https://github.com/jschicht/RcrdCarver"
packaged-by: [blackarch]
blackarch-builddate: "2019-11-23"
build-system: scripts
license: CCPL
meta-checked: "2026-11-09"
github-repo: "jschicht/RcrdCarver"
github-stars: 7
github-pushed: "2016-08-20"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 1.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# rcrdcarver

> Carve RCRD records — NTFS $LogFile entries — out of raw chunks of data such as unallocated space or memory dumps.

## What it's for

RcrdCarver (by Joakim Schicht, part of his NTFS forensic toolset alongside [[mftrcrd]], [[mftcarver]] and [[log-file-parser]]) scans a file for RCRD record signatures and validates each candidate's structure, recovering $LogFile records when no healthy $LogFile is available — for example from a memory dump, a damaged volume, or a chunk of unallocated space that may hold fragments of deleted shadow copies. Each hit is recovered twice — once with update-sequence fixups applied, once without — and false positives are separated into their own file, so the output is pre-triaged. Input must be a file; it does not read devices directly, and to interpret a carved RCRD stream you still need a full $LogFile parser such as [[log-file-parser]].

## Typical use cases

### Carve RCRD records from unallocated space

Recover $LogFile fragments left in freespace.

```
RcrdCarver.exe /InputFile:D:\unallocated.chunk /OutputPath:E:\carved
```

Outcome: writes four files into E:\carved — a verbose log, `.wfixups.RCRD` (records recovered with fixups applied), `.wofixups.RCRD` (records recovered without fixups) and `.false.positives.RCRD` — the wfixups file is the one to analyze for traces of deleted shadow copies.

### Carve from a memory dump

Extract RCRD records when the volume itself is unavailable.

```
RcrdCarver.exe /InputFile:D:\memory.dmp /OutputPath:E:\carved
```

Outcome: same three-way split output; the log records each hit's offset in the dump, which is what you cite when reporting where a recovered record came from. Omitting both parameters opens a file dialog instead of taking command-line input.

*No corpus evidence — derived from upstream documentation.*
