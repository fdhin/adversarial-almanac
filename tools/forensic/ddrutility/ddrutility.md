---
name: "ddrutility"
categories:
  - forensic
version: "2.8"
homepage: "http://sourceforge.net/projects/ddrutility/"
origin: "archstrike"
recipe: "archstrike-pkgbuild"
packaged-by: [archstrike, aur, nixpkgs]
build-system: make
license: GPL3
meta-checked: "2026-11-09"
github-repo: "fwiessner/ddrutility"
github-stars: 6
github-pushed: "2022-10-13"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# ddrutility

> Companion toolset for GNU ddrescue: maps read errors to files and builds domain maps so rescue runs copy only allocated data.

## What it's for

ddrutility is a small suite of utilities designed to work alongside GNU ddrescue when recovering data from failing drives or damaged images. Its tools consume the ddrescue mapfile and correlate unfinished or bad areas with the file system, reporting which files were affected, and can build a restricted domain mapfile so ddrescue copies only clusters actually in use (NTFS). A further utility interrogates disks directly through ATA/SCSI pass-through commands. The tools do not copy data themselves — ddrescue does that — and the bad-sector-to-file tools are designed to run against a recovered copy or image, not the original failing drive.

## Typical use cases

### List files hit by read errors on an NTFS volume

```
ddru_ntfsfindbad /dev/sdb rescue.mapfile
```

Outcome: parses the NTFS structures of the recovered disk or image, correlates them with the bad and unfinished areas listed in the ddrescue mapfile, and writes results listing the affected files and folders — the worklist for re-reading or restoring from backup. Add `-i <bytes>` when the partition starts at an offset inside a whole-disk image.

### Build a domain mapfile covering only allocated NTFS clusters

```
ddru_ntfsbitmap /dev/sdb2 used-domain.mapfile
```

Outcome: extracts the NTFS $Bitmap, runs ddrescue against the partition to map the used extents, and writes a domain mapfile; a second ddrescue run with `--domain-mapfile=used-domain.mapfile` then copies only allocated data. Add `-m mft-domain.mapfile` to also produce a domain file for the $MFT itself.

### Find affected files on other file systems (legacy tool)

```
ddru_findbad /dev/sdc rescue.mapfile
```

Outcome: shell-script implementation that walks the ddrescue mapfile with sleuthkit and ntfs-3g helpers and leaves its results in `results_summary`, `results_list`, `results_info`, and `results_debug` files. For NTFS prefer ddru_ntfsfindbad — it is much faster and needs no third-party utilities.

### Query a disk directly via pass-through commands

```
ddru_diskutility -V /dev/sdb
```

Outcome: issues direct pass-through commands (add `-p` for SCSI or `-P` for ATA pass-through) and prints detailed disk information — identity, capabilities, and error-related data useful when planning a recovery. Linux only.

*No corpus evidence — derived from upstream documentation.*
