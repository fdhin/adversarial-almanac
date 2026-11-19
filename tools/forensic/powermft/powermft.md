---
name: "powermft"
categories:
  - blackarch-forensic
version: "5.76574543"
homepage: "https://github.com/jschicht/PowerMft"
packaged-by: [blackarch]
blackarch-builddate: "2019-11-23"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "jschicht/PowerMft"
github-stars: 26
github-pushed: "2015-08-15"
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

# powermft

> Command-line editor that writes directly to individual NTFS $MFT records — change flags, timestamps, filenames and index entries at the raw record level.

## What it's for

PowerMft (by Joakim Schicht, adapted from his SetMace code) opens a file or MFT record on an NTFS volume and rewrites chosen fields of its $MFT record in place: the record header, $STANDARD_INFORMATION and $FILE_NAME attributes (timestamps, owner/security IDs, parent references), $OBJECT_ID GUIDs, $REPARSE_POINT data, $AttrDef entries, and the related $I30/$O/$R index entries. Reach for it to demonstrate or test anti-forensic timestamp and filename manipulation, or to experiment with NTFS internals on a scratch volume; for read-only MFT record dumps use [[mftrcrd]] instead. It writes to the physical volume, so it is destructive by design: on Vista and later it needs a non-system volume (or a dismounted one), field values are decimal unless a filename is given as 0x-prefixed hex, and a wrong value can cost the target file or volume.

## Typical use cases

### Dump a single MFT record

Inspect a record without modifying it.

```
PowerMft.exe /Target:C:\bootmgr /Verbose:1
```

Outcome: prints the raw $MFT record for C:\bootmgr to the console; no modification happens unless a field variable is also supplied on the same command line.

### Overwrite forensic timestamps

Set $SI/$FN timestamps and header sequence number directly in the record.

```
PowerMft.exe /Target:D:\folder /HdrSequenceNo:20 /FNMTime:"2000:01:01:00:00:00:789:1234"
```

Outcome: writes sequence number 20 into the record header and the given LastWrite time into the $FILE_NAME attribute of D:\folder, updating the parent $I30 index so the directory stays consistent.

### Target a record by MFT reference number

Edit a file whose name is invalid or unresolvable through the filesystem.

```
PowerMft.exe /Target:D:198 /SIQuotaCharged:256 /SIUSN:1000
```

Outcome: modifies $STANDARD_INFORMATION of MFT record 198 on volume D: in place — use record-number targeting when Windows cannot even parse the file's name.

### Rename a file inside $FILE_NAME

Change a name through the raw record path instead of the OS rename path.

```
PowerMft.exe /Target:D:\folder\file.ext /FNFileName:dumb.txt
```

Outcome: rewrites the filename in the $FILE_NAME attribute and updates the $I30 index of the parent directory, effectively renaming the file; for filenames Windows considers invalid, add /FNCoreFileName with the raw invalid name.

*No corpus evidence — derived from upstream documentation.*
