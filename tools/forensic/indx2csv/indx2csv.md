---
name: "indx2csv"
categories:
  - blackarch-forensic
version: "17.129a411e"
homepage: "https://github.com/jschicht/Indx2Csv"
packaged-by: [blackarch]
blackarch-builddate: "2019-11-23"
build-system: scripts
license: CCPL
meta-checked: "2026-11-09"
github-repo: "jschicht/Indx2Csv"
github-stars: 30
github-pushed: "2019-08-07"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# indx2csv

> Parse NTFS INDX records — $I30 directory indexes plus $ObjId and $Reparse indexes, including slack space — into CSV, log2timeline or bodyfile output.

## What it's for

Indx2Csv (jschicht) is a Windows parser for NTFS INDX index records. It decodes $I30 (the directory index built from $STANDARD_INFORMATION and $FILE_NAME values of a folder's children), the $O object-ID index and the $R reparse-point index, emitting every entry member to CSV. Its forensic value is slack: INDX attributes are reused rather than shrunk, so deleted directory entries survive in slack space and this tool can scan and decode them (`/Slack:1`). Input is ideally carved INDX records (its sibling IndxCarver separates records with and without fixups applied). It is a GUI program by default with a full command-line parameter set for batch runs; on Linux it runs under wine. The read-me documents exit codes (1 = nothing decoded, 4 = fragment write failed) for scripting.

## Typical use cases

### Parse a carved INDX file to CSV

Convert extracted INDX records into reviewable rows.

```
wine Indx2Csv64.exe /IndxFile:chunk.wfixups.INDX /OutputPath:outdir
```

Outcome: CSV files with all decoded entry members are written to `outdir` (default: current directory); records previously had fixups applied by the carver, which is the default `/Fixups:1` expectation — pass `/Fixups:0` for carver output that skipped fixups.

### Produce timeline-ready output

Emit log2timeline or bodyfile formats directly.

```
wine Indx2Csv64.exe /IndxFile:chunk.wfixups.INDX /OutputFormat:l2t /OutputPath:outdir
```

Outcome: the CSV is laid out in log2timeline format (`/OutputFormat:bodyfile` gives the bodyfile variant, `all` is the default); `/TSFormat`, `/TSPrecision` and `/TimeZone:+2.00`-style offsets control timestamp rendering.

### Recover deleted entries from INDX slack

Scan reused index blocks for stale directory entries.

```
wine Indx2Csv64.exe /IndxFile:chunk.INDX /Slack:1 /Unicode:1 /StrictNameCheck:1 /OutputPath:outdir
```

Outcome: slack space is scanned and surviving deleted entries are decoded alongside live ones; `/StrictNameCheck:1` suppresses the false positives that unicode slack scanning tends to produce.

### Validate and repair a fragment

Check a suspect fragment and write a repaired copy.

```
wine Indx2Csv64.exe /IndxFile:fragment.bin /VerifyFragment:1 /OutFragmentName:fixed.bin /OutputPath:outdir
```

Outcome: a lightweight validation runs on the fragment instead of the full parse, and the fixed-up fragment is written to `outdir/fixed.bin` for re-processing with `/ScanMode:1`–`15` (deeper levels scan harder for record boundaries).

*No corpus evidence — derived from upstream documentation.*
