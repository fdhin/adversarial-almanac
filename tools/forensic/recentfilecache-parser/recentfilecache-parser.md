---
name: "recentfilecache-parser"
categories:
  - blackarch-forensic
version: "2.5e22518"
homepage: "https://github.com/prolsen/recentfilecache-parser"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "prolsen/recentfilecache-parser"
github-status: "gone"
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
---

# recentfilecache-parser

> Python parser (rfcparse.py) for the Windows 7 RecentFileCache.bcf — the precursor artifact to AmCache, listing executable paths seen on the system.

## What it's for

On Windows 7 the Application Compatibility subsystem maintains RecentFileCache.bcf in C:\Windows\AppCompat, recording the paths of programs executed on the system before the richer AmCache.hve took over in Windows 8. This tool (rfcparse.py, by prolsen, with the file format documented in the Journey Into IR blog research it references) parses a captured .bcf file and lists those program paths. In an investigation it provides lightweight execution evidence: which binaries ran, from where, without needing registry hive parsing. It parses only this one artifact — for Windows 8+ systems use an AmCache parser instead.

## Typical use cases

### Parse a captured RecentFileCache.bcf

Extract the recorded program paths.

```
python rfcparse.py -f RecentFileCache.bcf
```

Outcome: prints one path per line — the executables the Application Compatibility infrastructure recorded, such as system utilities and vendor program paths — each a data point for execution tracing on a Windows 7 system.

### Review help and options

Confirm usage before running against evidence.

```
python rfcparse.py -h
```

Outcome: prints the usage summary: the tool takes exactly one input file via `-f`/`--recentfilecache` and has no other options, which keeps runs against evidence reproducible.

*No corpus evidence — derived from upstream documentation.*
