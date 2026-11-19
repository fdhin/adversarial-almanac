---
name: "mft2csv"
categories:
  - blackarch-forensic
version: "40.164eb224"
homepage: "https://github.com/jschicht/Mft2Csv"
packaged-by: [blackarch]
blackarch-builddate: "2019-11-23"
build-system: scripts
license: CCPL
meta-checked: "2026-11-09"
github-repo: "jschicht/Mft2Csv"
github-stars: 289
github-pushed: "2024-10-07"
github-archived: false
github-issues: 3
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# mft2csv

> Parses the NTFS $MFT into CSV by Joakim Schicht — full record and timestamp export from live volumes, images, extracted or carved $MFT files, with resident-data extraction.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Mft2Csv is the bulk parser at the hub of Joakim Schicht's Windows MFT suite — alongside [[mftcarver]], [[mftrcrd]] and [[mftref2name]]. It parses NTFS Master File Table records and logs them to CSV: every $MFT record describes one file or directory (its names, standard-information and filename timestamps — decodable to nanosecond precision — sizes and attribute layout), and Mft2Csv flattens those into sortable, filterable rows. Input can be a live mounted volume, an unmounted physical drive, a disk or partition image, an already-extracted $MFT file, a pseudo-$MFT carved out of a memory dump by [[mftcarver]], or a single-record .bin written by [[mftrcrd]] with `-w`. Output formats include log2timeline-style l2t CSV and body files for timeline tools, and it can extract resident data — content stored inside the record itself, roughly up to 700 bytes. It decodes records that already exist; when no healthy $MFT is available, carve one first with [[mftcarver]]. Windows GUI/CLI tool written in AutoIt; the BlackArch package ships the binaries under /usr/share/windows for use with Wine (running the bare wrapper without arguments just opens the GUI).

## Typical use cases

### Parse an extracted $MFT file

```
Mft2Csv.exe /MftFile:c:\temp\$MFT /TimeZone:2.00 /RecordSize:4096 /TSFormat:1 /TSPrecision:MilliSec /Unicode:1 /ScanSlack:1
```

Outcome: writes a CSV (default separator `|`) with one row per record and timestamps decoded to the chosen timezone and precision; /ScanSlack also scans record slack for older $I30 entries. Defaults work out fine in most cases and are MySQL-import compatible.

### Parse the $MFT of a live mounted volume

```
Mft2Csv.exe /Volume:e: /TSFormat:2 /TSPrecision:None
```

Outcome: reads the $MFT directly from the mounted volume and produces the CSV without extracting anything first — live-system triage.

### Produce a body file for timeline analysis

```
Mft2Csv.exe /MftFile:c:\temp\$MFT /OutputFormat:BodyFile
```

Outcome: emits timeline-tool body-file format instead of the default CSV (`l2t` and `all` are the other valid values), ready for mactime-style processing of file activity over time.

### Extract resident data while parsing

```
Mft2Csv.exe /MftFile:c:\temp\$MFT /ExtractResidentData:1 /OutputPath:c:\temp\MftOutput
```

Outcome: besides the CSV, every resident data stream (regular files and alternate data streams up to about 700 bytes) is written under the output path, with the $MFT offset prefixed to the filename, a [DEL] marker for deleted entries and ADS_ prefixes for data streams — e.g. [0x00228000][DEL]import.reg.

### Decode $MFT records carved from a memory dump

```
Mft2Csv.exe /MftFile:c:\temp\Carver_MFT_2015-02-14_21-46-54.wofixups.MFT /BrokenMft:1 /SkipFixups:1
```

Outcome: parses a pseudo-$MFT produced by [[mftcarver]]; for the .wofixups output enable both /BrokenMft and /SkipFixups (for the .wfixups output, /BrokenMft alone), and records whose update-sequence fixups never validated still decode into CSV rows.
