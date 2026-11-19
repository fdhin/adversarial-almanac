---
name: "log-file-parser"
categories:
  - blackarch-forensic
version: "60.c7a0ae7e"
homepage: "https://github.com/jschicht/LogFileParser"
packaged-by: [blackarch]
blackarch-builddate: "2019-11-23"
build-system: scripts
license: CCPL
meta-checked: "2026-11-09"
github-repo: "jschicht/LogFileParser"
github-stars: 221
github-pushed: "2026-06-05"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 5.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 1
gh-activity-checked: "2026-11-09"
---

# log-file-parser

> Decode the NTFS $LogFile transaction journal into CSV files and an SQLite database.

## What it's for

LogFileParser (jschicht) decodes the NTFS `$LogFile` — the filesystem's circular transaction journal — and dumps what it finds: transaction records and headers, NTFS attribute changes (file creations, renames, deletions, resident data updates), index and bitmap operations, across a family of CSVs plus an `ntfs.db` SQLite database. Because `$LogFile` is circular it holds only recent history — hours on a busy system drive, more on a quiet data volume — but it can show file activity that no longer exists in the $MFT. The output is deliberately low-level and expects NTFS knowledge. It is a Windows console application (packaged behind a `log-file-parser` wrapper that runs `LogFileParser64.exe` under wine, same switches); started with no parameters it opens a GUI, and in `/switch:value` command-line mode it sets process error levels for batch scripting.

## Typical use cases

### Parse an extracted $LogFile

The basic decode of a copied-out journal.

```
log-file-parser /LogFileFile:C:\cases\$LogFile /OutputPath:C:\cases\lfp-out /TimeZone:0.00
```

Outcome: parses the journal and writes `LogFile.csv`, the supporting CSVs (transaction headers, dirty page tables, bitmap operations, USN journal records and more) and the `ntfs.db` SQLite database to the output directory, with timestamps adjusted to the chosen UTC offset.

### Emit timeline formats

Produce log2timeline and bodyfile rows directly.

```
log-file-parser /LogFileFile:C:\cases\$LogFile /OutputPath:C:\cases\lfp-out /OutputFormat:all
```

Outcome: CSV output additionally in log2timeline, bodyfile and full formats (valid values are `l2t`, `BodyFile`, `all`) for import into timeline tools.

### Join MFT records into the output

Correlate journal transactions with filenames.

```
log-file-parser /LogFileFile:C:\cases\$LogFile /MftCsvFile:C:\cases\$MFT.csv /OutputPath:C:\cases\lfp-out
```

Outcome: in addition to the standard output, `LogFileJoined.csv` is produced with filename information from an mft2csv CSV joined onto the transaction records.

### Validate and repair a carved fragment

Work on a $LogFile fragment carved from slack space or memory.

```
log-file-parser /LogFileFragmentFile:C:\cases\fragment.bin /OutputPath:C:\cases\lfp-out /VerifyFragment:1
```

Outcome: runs fragment validation only and writes a repaired copy (default name `OutFragment.bin`, override with `/OutFragmentName:`); exit code 3 signals the fragment failed validation.

### Parse rebuilt RCRD records

Re-run the parser over records reassembled from slack space.

```
log-file-parser /LogFileFile:C:\cases\lfp-out\FragmentsRCRDCollection.bin /OutputPath:C:\cases\lfp-out /SkipFixups:1 /BrokenLogFile:1
```

Outcome: parses the reconstructed record collection with fixup application and several validation checks bypassed — the mode intended for reassembled or carved `$LogFile` data; error level 1 means nothing decodable was found.

*No corpus evidence — derived from upstream documentation.*
