---
name: "exescan"
categories:
  - blackarch-binary
version: "1.ad993e3"
homepage: "https://github.com/cysinfo/Exescan"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "cysinfo/Exescan"
github-stars: 21
github-pushed: "2018-12-14"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# exescan

> Python 2 PE triage scanner: hashes, structure anomalies and malware-typical API usage in Portable Executable files.

## What it's for

Exescan (Amit Malik, 2011–2012) is a static analyzer for PE files used for
quick malware triage. Given a file or a whole directory it computes
MD5/SHA1/SHA256 hashes and reports basic PE information, structural anomalies
that indicate tampering or packing, import/export tables, and imports matching
APIs commonly abused by malware. It is a small Python 2 script (requires
Python >= 2.5, < 3.0 and the pefile/peutils libraries) — expect dependency
friction on modern systems; BlackArch wraps it as `exescan` running
`exescan.py`. It flags suspicious traits, not malware families: there are no
signatures or YARA rules.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Summarize a PE with hashes

```
exescan -b suspicious.exe
```

Outcome: prints basic information for the file, including its MD5, SHA1 and
SHA256 digests, for recording in triage notes or lookup in threat-intel
services.

### Detect structural anomalies in a PE

```
exescan -a suspicious.exe
```

Outcome: runs the advanced scan and reports anomalies in the PE layout —
header inconsistencies and unusual structures that typically come from packers
or hand-edited binaries.

### Screen a directory for malware-typical API usage

```
exescan -m samples/
```

Outcome: lists the imports of every PE in the directory that match APIs
commonly used by malware, narrowing which samples deserve a closer look.

### List import and export tables

```
exescan -i suspicious.exe
```

Outcome: prints the import and export tables of the file — a quick view of the
functional surface the sample pulls in. Use `-p` for the raw PE header dump.
