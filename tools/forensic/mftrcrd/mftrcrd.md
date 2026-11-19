---
name: "mftrcrd"
categories:
  - blackarch-forensic
version: "16.35c3ac2f"
homepage: "https://github.com/jschicht/MftRcrd"
packaged-by: [blackarch]
blackarch-builddate: "2019-11-23"
build-system: scripts
license: CCPL
meta-checked: "2026-11-09"
github-repo: "jschicht/MftRcrd"
github-stars: 12
github-pushed: "2017-05-20"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 1.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# mftrcrd

> Single $MFT record decoder by Joakim Schicht — decodes one NTFS record from a path, record number or volume offset, optionally hexdumping attributes or writing the raw record to disk.

*No corpus evidence — derived from upstream documentation.*

## What it's for

MFTRCRD decodes one NTFS Master File Table record at a time and prints its parsed contents — filenames, standard-information and filename timestamps, sizes, flags and attribute layout — to the console. Where [[mft2csv]] bulk-parses a whole $MFT, MFTRCRD targets a single record, addressed three ways: by a valid file or folder path, by record (index) number on a volume, or by volume and offset. With `-a` it also prints a formatted hexdump of the record and of each individual attribute, and with `-w` it writes the raw record out as a binary file for further parsing in [[mft2csv]] — the documented path for decoding a single record into CSV. Part of Joakim Schicht's MFT suite ([[mft2csv]], [[mftcarver]], [[mftref2name]]). Windows console tool written in AutoIt; the BlackArch package ships the binaries under /usr/share/windows for use with Wine.

## Typical use cases

### Decode the record for a specific file

```
MFTRCRD C:\boot.ini -d indxdump=off 1024 -s
```

Outcome: prints the decoded $MFT entry for that path — timestamps, sizes and attribute details — to the console; `-s` skips writing the raw record to disk.

### Decode a record by number with a full hexdump

```
MFTRCRD C:0 -a indxdump=off 1024 -s
```

Outcome: decodes record 0 on the C: volume (the $MFT itself) and additionally prints a formatted hexdump of the record and of each of its attributes.

### Extract one record as a binary file

```
MFTRCRD C:\boot.ini -d indxdump=off 1024 -w
```

Outcome: writes the raw record in binary format to the current directory; point Mft2Csv at that .bin (`Mft2Csv.exe /MftFile:...Record.bin`) to render the single record as a CSV row.

### Hexdump the INDX records of a directory

```
MFTRCRD C:\WINDOWS -d indxdump=on 1024 -s
```

Outcome: decodes the directory's record and also hexdumps its complete INDX (index allocation) records — beware that indxdump=on can generate a significant amount of console output for certain directories.
