---
name: "secure2csv"
categories:
  - blackarch-forensic
version: "10.119eefb0"
homepage: "https://github.com/jschicht/Secure2Csv"
packaged-by: [blackarch]
blackarch-builddate: "2019-11-23"
build-system: scripts
license: CCPL
meta-checked: "2026-11-09"
github-repo: "jschicht/Secure2Csv"
github-stars: 23
github-pushed: "2022-02-24"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# secure2csv

> Decode the NTFS $Secure file's security descriptors ($SDS) into CSV.

## What it's for

Secure2Csv (by jschicht) parses the `$SDS` stream of NTFS's `$Secure` system
file — the volume-wide pool of security descriptors — and dumps every
descriptor to CSV: owner and group SIDs, control flags, and each SACL/DACL ACE
with type, flags, access mask and trustee SID. Supplying the `$SII` index as
well speeds up processing (the `$SDS` stores descriptors twice) and enables
extraction of three flavors of slack data. It is a Windows tool with an
intuitive GUI that launches when no arguments are given; the same switches work
on the command line. The input streams must already be extracted from the
volume with other forensic tooling before running it.

## Typical use cases

### Dump all security descriptors

Convert the descriptor pool into a spreadsheet-friendly CSV.

```
Secure2Csv.exe /SDSFile:c:\temp\$Secure[ADS_$SDS] /OutputPath:c:\temp
```

Outcome: writes a timestamped CSV in the output path where each row is one
descriptor from `$SDS` with its offset, hash, SecurityId, owner/group SIDs and
SACL/DACL ACEs.

### Speed up parsing and capture slack data

Add the `$SII` index to the run.

```
Secure2Csv.exe /SDSFile:c:\temp\$Secure[ADS_$SDS] /SIIFile:c:\temp\$Secure_9_$INDEX_ALLOCATION_$SII.bin /OutputPath:c:\temp
```

Outcome: uses the index to skip duplicated descriptors, and additionally
writes `_sds_slack1.bin`, `_sds_slack2.bin` and `_sds_slack3.bin` holding the
data between entries, inside over-sized entries, and beyond the last valid
entry — candidates for further carving.

### Choose CSV and ACE separators

Keep multi-ACE descriptors readable in the CSV.

```
Secure2Csv.exe /SDSFile:c:\temp\$Secure[ADS_$SDS] /OutputPath:c:\temp /Separator:% /AceSeparator:!
```

Outcome: fields are separated by `%` and the ACEs inside one descriptor by `!`,
so an ACE list never collides with the field delimiter.

*No corpus evidence — derived from upstream documentation.*
