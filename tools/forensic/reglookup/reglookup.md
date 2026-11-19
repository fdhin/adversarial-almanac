---
name: "reglookup"
categories:
  - blackarch-forensic
version: "1.0.1+svn296.2.r0.gcab83db"
homepage: "https://projects.sentinelchicken.org/reglookup"
packaged-by: [blackarch, kali]
blackarch-builddate: "2026-10-31"
build-system: python
license: GPL-3.0-or-later
meta-checked: "2026-11-09"
github-repo: "pld-linux/reglookup"
github-stars: 1
github-pushed: "2025-07-15"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# reglookup

> Read, filter, and convert Windows NT registry hives into CSV from the command line.

## What it's for

reglookup (by Timothy D. Morgan) is a command-line utility for reading and querying Windows NT-based registry files and printing their contents to stdout in a CSV-like format, with path and type filters to narrow the output. It works on offline hives grabbed from `%SystemRoot%/system32/config` or a user profile (NTUSER.DAT). The package also ships reglookup-recover, which scavenges deleted data structures from a hive, and reglookup-timeline, which produces an MTIME-sorted timeline across multiple hives. It parses and reports; it does not edit hives.

## Typical use cases

### Dump an entire hive as CSV

Extract everything from a system hive for scripted analysis.

```
reglookup /mnt/evidence/C/Windows/system32/config/SYSTEM > system.csv
```

Outcome: every key and value is written to stdout as CSV (quoted, with %XX encoding for special bytes) including a header row naming the columns.

### Filter output to a registry subtree

Only look at entries under a specific key path.

```
reglookup -p /ControlSet002/Services /mnt/evidence/C/Windows/system32/config/SYSTEM
```

Outcome: only keys and values below the given path prefix are printed, so service configurations can be reviewed without dumping the whole hive.

### Filter by value type

Pull only values of a given registry data type.

```
reglookup -t BINARY -p /ControlSet002/Services SYSTEM
```

Outcome: only BINARY-typed elements under Services are listed; `-t` accepts NONE, SZ, EXPAND_SZ, BINARY, DWORD, DWORD_BE, LINK, MULTI_SZ, QWORD, and KEY.

### Recover deleted data from a hive

Scour the hive for structures no longer referenced by allocated keys.

```
reglookup-recover SYSTEM > system_deleted.csv
```

Outcome: deleted keys and values found in unallocated space are output in the same CSV style; add `-l` to also list leftover cells that could not be interpreted, `-r` to append raw cell contents.

### Build an MTIME timeline across hives

Sort key modification times from several hives into one timeline.

```
reglookup-timeline SYSTEM SOFTWARE NTUSER.DAT > registry_timeline.csv
```

Outcome: emits MTIME,FILE,PATH rows sorted ascending by modification time across all given hives — key events from different sources interleaved chronologically for timeline work.

*No corpus evidence — derived from upstream documentation.*
