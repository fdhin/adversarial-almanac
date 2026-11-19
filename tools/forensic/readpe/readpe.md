---
name: "readpe"
categories:
  - forensics
version: "0.85.1-1+b2"
homepage: "https://github.com/mentebinaria/readpe/wiki"
origin: "kali"
recipe: "kali-dsc"
packaged-by: [kali, aur]
build-system: make
meta-checked: "2026-11-09"
github-repo: "mentebinaria/readpe"
github-stars: 791
github-pushed: "2026-08-25"
github-archived: false
github-issues: 20
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 5
gh-activity-checked: "2026-11-09"
---

# readpe

> Open-source command-line toolkit (formerly pev, by Mente Binária) for inspecting Windows PE binaries: headers, imports/exports, resources and suspicious traits.

## What it's for

readpe is the renamed pev toolkit — a multiplatform suite built on the libpe library for analyzing PE32/PE32+ files (see [[pev]] for the old package name still used by some distros). Its main binary prints every part of a PE image — DOS/COFF/optional headers, sections, data directories, imports, exports — in text, CSV, HTML, JSON or XML, which makes it a good scripted triage front-end. The package also ships sibling binaries from the same build: pescan for suspicious characteristics, pehash for hashes, pedis to disassemble, pestr for strings, peres for resources, pepack to detect packers, peldd for dependent libraries, and rva2ofs/ofs2rva for address conversion. It parses the file as data; it neither executes nor emulates the target.

## Typical use cases

### Dump a PE's headers and metadata

Full structural view of an unknown sample.

```
readpe ./sample.exe
```

Outcome: prints the complete default output — DOS, COFF and optional headers, section table, data directories — enough to state compiler hints, timestamps, target architecture and section characteristics at a glance.

### Triage imports and exports

See what a binary pulls in and offers out.

```
readpe -i ./sample.exe
readpe -e ./sample.exe
```

Outcome: `-i` lists the imported functions per DLL and `-e` the exported symbols, the fastest way to spot capability hints (network, process, registry APIs) or confirm a DLL's attack surface.

### Get machine-readable output for pipelines

Feed PE metadata into other tooling.

```
readpe -f json ./sample.exe
```

Outcome: emits the same information as a JSON document (`-f` also accepts text, csv, html and xml), suitable for jq processing or ingestion into a triage pipeline.

### Flag suspicious characteristics

Quick heuristics pass over a sample.

```
pescan -v ./sample.exe
```

Outcome: reports suspicious traits (such as abnormal section characteristics or entry-point anomalies) with extra detail under `-v`, directing where deeper manual analysis should start.

*No corpus evidence — derived from upstream documentation.*
