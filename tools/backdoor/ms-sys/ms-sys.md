---
name: "ms-sys"
categories:
  - blackarch-backdoor
version: "2.8.0"
homepage: "http://ms-sys.sourceforge.net/"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2024-06-16"
build-system: make
license: GPL2
meta-checked: "2026-11-09"
github-repo: "pbatard/ms-sys"
github-stars: 33
github-pushed: "2020-01-31"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# ms-sys

> Writes Microsoft-compatible master boot records and FAT/NTFS partition boot records from Linux — the `fdisk /mbr` and `sys d:` equivalents.

## What it's for

Creating or restoring Windows boot records on disks and floppy/CD images without booting DOS or Windows. It writes MBRs spanning Windows 95 through Windows 7, FAT12/16/32 and NTFS partition boot records, plus alternates (FreeDOS, ReactOS, Syslinux, Grub4Dos, zeroed MBR). The classic workflow is restoring a backed-up Windows installation to a fresh disk from Linux: write the MBR, write the partition boot record, then lay the files down. It never copies system files — `io.sys` and friends remain your job. Running it with no options inspects the current boot record without writing anything, which is the safe first command; writing the wrong record type can leave a disk unbootable, and `-f` forces past its sanity checks.

## Typical use cases

### Inspect the boot record on a device (safe default)

```
ms-sys /dev/sda
```

Outcome: prints which MBR or partition boot record type it detects on the device and writes nothing — use it to identify what you are about to overwrite before committing to a write.

### Write a Windows MBR to a fresh disk

```
sudo ms-sys -7 /dev/sda
```

Outcome: writes a Windows 7 MBR to the disk (`-m` selects the Windows 2000/XP/2003 MBR, `-9` the Win95B/98/SE/ME MBR, and `-w` lets ms-sys auto-select the record). Only the boot code area is rewritten; the existing partition table is preserved.

### Make a restored FAT32 partition bootable

```
sudo ms-sys -wp /dev/sda1
```

Outcome: writes an automatically selected partition boot record plus partition info (hidden sectors, heads, drive id) to the partition — the two steps the upstream restore walkthrough performs as `-w` then `-p`, combined. Run this after laying down a Windows backup onto a freshly formatted partition so it actually boots.

### Write an NTFS partition boot record

```
sudo ms-sys -n /dev/sda1
```

Outcome: writes the Windows 7 NTFS partition boot record to the partition. Upstream cautions that a Windows 7 NTFS record alone is rarely enough to make a partition bootable — treat it as one piece of a restore, not the fix.

### Zero out an MBR

```
sudo ms-sys -z /dev/sda
```

Outcome: replaces the MBR with zeroes — boot code and partition table together — leaving the disk's first sector clean (e.g. before re-imaging, or to invalidate stale boot code). Any partitions on the disk become unaddressable until the table is restored, so be certain of the device name.

*No corpus evidence — derived from upstream documentation.*
