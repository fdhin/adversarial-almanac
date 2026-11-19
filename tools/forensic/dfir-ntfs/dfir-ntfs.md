---
name: "dfir-ntfs"
categories:
  - blackarch-forensic
version: "1.1.20"
homepage: "https://github.com/msuhanov/dfir_ntfs"
packaged-by: [blackarch]
blackarch-builddate: "2026-05-02"
build-system: python
license: GPL-3.0-or-later, custom:unknown
meta-checked: "2026-11-09"
github-repo: "msuhanov/dfir_ntfs"
github-stars: 244
github-pushed: "2025-10-31"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# dfir-ntfs

> Python 3 forensic parser for NTFS/FAT volumes: $MFT, $UsnJrnl, $LogFile, volume shadow copies, and memory-dump MFT carving.

## What it's for

dfir_ntfs (by Maxim Suhanov) extracts as much data as possible from NTFS metadata — $MFT, $UsnJrnl:$J, and $LogFile — and parses whole volumes, volume images, volume shadow copies, and FAT12/16/32 plus exFAT file systems. It ships as a Python 3 library with three command-line tools (`ntfs_parser`, `vsc_mount`, `fat_parser`) that write CSV/TXT output suited to timelines and evidence review. All timestamps are reported in UTC using MACE notation (modified, accessed, created, $MFT entry modified). It is a read-only parser — it does not repair or modify file systems. On BlackArch the commands are installed with dashes in their names (`ntfs-parser`, `vsc-mount`, `fat-parser`).

## Typical use cases

### Parse $MFT records to CSV

```
ntfs_parser --mft mft.dump mft-records.csv
```

Outcome: writes one CSV row per $MFT record with names, attributes, and UTC MACE timestamps — directly usable as timeline input.

### Parse the USN change journal

```
ntfs_parser --usn mft.dump usnjrnl-j.dump usn-records.csv
```

Outcome: combines the $MFT with the $UsnJrnl:$J stream and emits a CSV of USN records with filenames and timestamps documenting the volume's change history.

### Extract every MFT entry from a whole-disk image

```
ntfs_parser --all-mft disk.img 1048576 all-mft-records.csv
```

Outcome: opens the volume at the given byte offset (here the common 1 MiB first-partition start) and dumps every $MFT entry it can recover from the volume to CSV.

### Carve $MFT records from a memory image

```
ntfs_parser --mem memory.dmp mft-from-mem.csv
```

Outcome: scans a raw Windows memory image for $MFT records and writes them to CSV — file-system evidence when no disk image is available.

### List and mount volume shadow copies

```
vsc_mount --list disk.img 1048576
vsc_mount --mount disk.img 1048576 1 /mnt/vsc
```

Outcome: `--list` prints each shadow copy's stack position and UTC timestamp; `--mount` exposes the chosen shadow copy through FUSE as `image.raw` under the mount point for read-only browsing. Requires the llfuse extra.

### Parse FAT12/16/32 or exFAT volumes

```
fat_parser disk.img 1048576 fat-entries.csv
```

Outcome: walks the FAT or exFAT file system at the byte offset and writes a CSV of entries with deleted/directory flags, paths, timestamps, and sizes.

*No corpus evidence — derived from upstream documentation.*
