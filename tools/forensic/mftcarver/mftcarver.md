---
name: "mftcarver"
categories:
  - blackarch-forensic
version: "9.7bfcc0a2"
homepage: "https://github.com/jschicht/MftCarver"
packaged-by: [blackarch]
blackarch-builddate: "2019-11-23"
build-system: scripts
license: CCPL
meta-checked: "2026-11-09"
github-repo: "jschicht/MftCarver"
github-stars: 16
github-pushed: "2016-08-21"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# mftcarver

> Carves raw $MFT records out of unstructured data by Joakim Schicht — memory dumps, unallocated space or slack — producing a pseudo-$MFT for [[mft2csv]] to decode.

*No corpus evidence — derived from upstream documentation.*

## What it's for

MftCarver scans a chunk of data for NTFS $MFT records, matching on the record signature and then validating the record structure — for situations where no healthy $MFT is accessible, such as a memory dump or a damaged volume. It is part of Joakim Schicht's MFT suite ([[mft2csv]], [[mftrcrd]], [[mftref2name]]). By default it first attempts to apply the update-sequence fixups of each candidate record and, when that fails, retries with fixups skipped, splitting the finds into three quality tiers plus a verbose log; the output files form a pseudo-$MFT that [[mft2csv]] then decodes into CSV — the documented pipeline for pulling file-system history out of a memory dump. It recovers records, not files: resident-data extraction and timestamp decoding happen downstream in Mft2Csv. Windows CLI/GUI tool written in AutoIt; the BlackArch package ships the binaries under /usr/share/windows for use with Wine.

## Typical use cases

### Carve $MFT records from a memory dump

```
MftCarver.exe /InputFile:c:\temp\memdump.bin /RecordSize:1024
```

Outcome: scans the dump at the default 1024-byte record size and writes four files to the program directory — a timestamped .log, .wfixups.MFT (records whose fixups validated), .wofixups.MFT (records dumped without fixups) and .false.positives.MFT; check the log for verbose findings, especially false-positive offsets.

### Carve unallocated space to another location

```
MftCarver.exe /InputFile:c:\unallocated.chunk /OutputPath:e:\temp /RecordSize:4096
```

Outcome: same signature-and-validation carve over a chunk of unallocated disk space, writing the output set under e:\temp with 4096-byte records for volumes formatted with the larger record size.

### Reassemble MFT fragments

```
MftCarver.exe /InputFile:c:\slack.chunk /OutputPath:e:\temp /RecordSize:1024 /VerifyFragment:1 /OutFragmentName:MftFragments.bin /CleanUp:1
```

Outcome: instead of the four-file set, writes the single file e:\temp\MftFragments.bin with the extracted fragment data; decode it with Mft2Csv running with skip fixups and Broken $MFT enabled.
