---
name: "mftref2name"
categories:
  - blackarch-forensic
version: "6.7df9eebb"
homepage: "https://github.com/jschicht/MftRef2Name"
packaged-by: [blackarch]
blackarch-builddate: "2019-11-23"
build-system: scripts
license: CCPL
meta-checked: "2026-11-09"
github-repo: "jschicht/MftRef2Name"
github-stars: 1
github-pushed: "2016-04-30"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 1.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# mftref2name

> MFT reference-number resolver by Joakim Schicht — converts an NTFS file reference number to a full path, or a path back to its reference number.

*No corpus evidence — derived from upstream documentation.*

## What it's for

A small utility from Joakim Schicht's MFT suite ([[mft2csv]], [[mftcarver]], [[mftrcrd]]) that converts an MFT reference (file index) number to a file name and path, or the other way around, on a live NTFS volume. It takes a single parameter — the target — and should resolve any reference number present in the $MFT, or any filename and path valid on the filesystem, including hidden system files. That makes it the lookup companion to [[mft2csv]] and USN-journal/INDX analysis, which hand you bare reference numbers that need names. Limitation: if the target has been marked as deleted, resolving the path is aborted, as the result may turn out incorrect. Windows console tool written in AutoIt; the BlackArch package ships the binaries under /usr/share/windows for use with Wine.

## Typical use cases

### Resolve a reference number to a path

```
MftRef2Name.exe C:3366
```

Outcome: looks up record 3366 in the C: volume's $MFT and prints the file's name and full path — turning a bare reference number from $MFT, USN journal or INDX analysis back into a filename.

### Resolve a path to its reference number

```
MftRef2Name.exe C:\$Extend\$UsnJrnl
```

Outcome: prints the MFT reference number for that path (the USN journal file, resolved here as a hidden system file), giving the number to grep for in parsed $MFT or USN records.
