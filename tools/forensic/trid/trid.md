---
name: "trid"
categories:
  - blackarch-forensic
version: "2.48"
homepage: "http://mark0.net/soft-trid-e.html"
packaged-by: [blackarch, aur]
blackarch-builddate: "2026-06-29"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
worth: 0.8
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# trid

> File-type identifier driven by an extensible database of binary signatures.

## What it's for

TrID identifies unknown files from their binary signatures and prints candidate types ranked by probability. Instead of hard-coded rules it uses a separate, frequently updated definitions file (triddefs.trd, refreshable with `--update`), so it recognizes far more formats than a magic-byte lookup and can be trained on new formats with the companion TrIDScan. Reach for it in forensic triage and file recovery when the extension and the file's actual content disagree or are missing.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Identify an unknown file

Ask what a mystery file really is.

```
trid unknown.bin
```

Outcome: loads triddefs.trd from the current directory and prints ranked matches with probabilities, e.g. `70.7% (.DOC) Microsoft Word document` followed by the runner-up types.

### Batch-rename recovered files by detected type

Fix extensions for a directory of carved or rescued files.

```
trid recovered/* -ae
```

Outcome: scans every file in recovered/ and appends its guessed extension (`FILE0001.CHK` becomes `FILE0001.CHK.doc`), reporting `N file(s) renamed.`; `-ce` replaces the existing extension instead of appending one.

### Analyze a prebuilt file list

Process exactly the files another tool selected.

```
trid -f filelist.txt
```

Outcome: identifies only the paths listed one per line in filelist.txt, so an entire tree or an arbitrary subset can be checked without pointing trid at everything.

### Get detailed output for the top matches

Trim the report to what matters.

```
trid -v -n 2 unknown.bin
```

Outcome: verbose mode adds per-match detail such as MIME type and definition author, and `-n 2` limits output to the top two candidates instead of the default five.
