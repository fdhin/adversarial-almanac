---
name: "indxcarver"
categories:
  - blackarch-forensic
version: "5.dee36608"
homepage: "https://github.com/jschicht/IndxCarver"
packaged-by: [blackarch]
blackarch-builddate: "2019-11-23"
build-system: scripts
license: CCPL
meta-checked: "2026-11-09"
github-repo: "jschicht/IndxCarver"
github-stars: 10
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

# indxcarver

> Carve NTFS INDX ($I30) directory records out of raw data such as memory dumps, unallocated space or damaged volumes.

## What it's for

Windows utility by jschicht (AutoIt-compiled; on Linux it runs through Wine) that scans an input file for INDX record signatures, validates the record structure, and writes every recovered record to disk. It is meant for the cases where no healthy NTFS is available to read $I30 attributes from — a memory image, a chunk of unallocated space, or a corrupted volume. Fixup handling (update sequence array verification and application) is attempted first and retried without fixups on failure. Output is split three ways — records with fixups applied, records without, and false positives — plus a verbose log. It extracts raw INDX records; it does not parse them into filenames and timestamps — use [[indxparse]] for parsing INDX/$I30 data.

## Typical use cases

### Carve INDX records from a memory dump

Recover directory-index records from a raw memory image.

```
IndxCarver.exe /InputFile:c:\memdump.bin
```

Outcome: writes `Carver_Indx_<timestamp>.wfixups.INDX`, `.wofixups.INDX`, `.false.positives.INDX` and a `.log` file to the program directory (default when no output path is given); the log lists offsets and verdicts for every candidate record.

### Carve from unallocated space into a target folder

Direct the carved records somewhere other than the program directory.

```
IndxCarver.exe /InputFile:c:\unallocated.chunk /OutputPath:e:\temp
```

Outcome: same three-way split of carved records plus log file, written under `e:\temp`.

### Run interactively under Wine on Linux

Launch the tool without arguments to pick the input file from a dialog.

```
indxcarver
```

Outcome: the BlackArch wrapper starts the bundled `IndxCarver64.exe` under Wine and a file-open dialog is presented; output defaults to the program directory.

*No corpus evidence — derived from upstream documentation.*
