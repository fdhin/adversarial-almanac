---
name: "usnjrnl2csv"
categories:
  - blackarch-forensic
version: "29.1ecbddc"
homepage: "https://github.com/jschicht/UsnJrnl2Csv"
packaged-by: [blackarch]
blackarch-builddate: "2022-12-02"
build-system: scripts
license: CCPL
meta-checked: "2026-11-09"
github-repo: "jschicht/UsnJrnl2Csv"
github-stars: 126
github-pushed: "2022-11-27"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# usnjrnl2csv

> Converts an extracted NTFS $UsnJrnl:$J stream into CSV, bodyfile or log2timeline output.

## What it's for

UsnJrnl2Csv (Joakim Schicht) parses the NTFS USN change journal — the `$Extend\$UsnJrnl:$J` alternate data stream that records metadata changes (create, delete, rename, data writes) for every file on the volume — and writes the records as CSV, mactime bodyfile, or log2timeline body format. It handles USN_RECORD_V2 and V3 (not V4), and exposes timezone, timestamp format/precision and CSV separator/quoting controls. A brute-force ScanMode recovers structurally valid USN records from damaged or carved input at the cost of speed and some false positives. It is a Windows GUI tool with a complete command-line switch set, and it does not extract the journal itself: the `$J` stream must be pulled off the volume first (the same author's ExtractUsnJrnl is the recommended extractor — see [[extractusnjrnl]]). For a cross-platform Python parser of the same artifact, see [[usnparser]].

## Typical use cases

### Convert a journal dump to CSV

Baseline processing of an extracted journal.

```
UsnJrnl2Csv.exe /UsnJrnlFile:C:\temp\$UsnJrnl_$J.bin
```

Outcome: parses the extracted `$J` stream and writes CSV output (default format "all", UTC timestamps, nanoSecond precision) next to the program or under `/OutputPath`; every record's fields — timestamp, MFT references, file name, reason flags — land in the CSV.

### Produce a timeline-ready bodyfile

Output for mactime/log2timeline pipelines, in a chosen timezone.

```
UsnJrnl2Csv.exe /UsnJrnlFile:C:\temp\$UsnJrnl_$J.bin /OutputPath:C:\temp\out /TimeZone:2.00 /OutputFormat:bodyfile /TSFormat:1 /TSPrecision:MilliSec
```

Outcome: converts the journal at +02:00 into mactime bodyfile format with millisecond-precision timestamps in `C:\temp\out`, ready to merge into a super-timeline; `/OutputFormat:l2t` gives log2timeline body instead.

### Carve records from damaged input

Recover USN records from fragments or carved data.

```
UsnJrnl2Csv.exe /UsnJrnlFile:C:\temp\fragment.bin /ScanMode:1 /OutputPath:C:\temp\out
```

Outcome: brute-force scans the input for any structurally valid USN record regardless of page alignment — far slower than normal mode but effective on damaged journals; exit code 1 means no records could be decoded at all.

*No corpus evidence — derived from upstream documentation.*
