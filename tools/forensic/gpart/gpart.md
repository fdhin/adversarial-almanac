---
name: "gpart"
categories:
  - forensics
version: "0.3-6"
homepage: "https://github.com/baruch/gpart"
origin: "arch"
arch-repo: "extra"
packaged-by: [arch, kali, nixpkgs]
arch-builddate: "2026-04-09"
build-system: make
license: GPL
meta-checked: "2026-11-09"
github-repo: "baruch/gpart"
github-stars: 31
github-pushed: "2023-07-24"
github-archived: false
github-issues: 8
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# gpart

> Reconstruct a lost MBR partition table by scanning the disk for filesystem signatures.

## What it's for

gpart (by Michail Brzitwa, 1999–2001; the GitHub repo is the maintained mirror) guesses the PC-style (MBR) partition layout of a disk whose primary partition table was damaged, overwritten or wiped, while the filesystem data itself is still there. It scans a raw device or a disk image sector by sector and asks built-in guessing modules — FAT, NTFS/HPFS, ext2, Linux swap, BSD disklabels, Minix, ReiserFS, XFS, Btrfs, LVM, QNX4, BeOS and more — whether a filesystem they recognize could start at each sector, checks the resulting guesses for overlap and consistency, and assembles a new primary partition table. It only reconstructs the primary/extended layout — it cannot rebuild a damaged chain of logical partitions — and it never writes anything to the disk unless you explicitly pass `-W`.

## Typical use cases

### Guess the partitions of a damaged disk

Read-only recovery scan of a whole disk.

```
gpart -v /dev/sdb
```

Outcome: prints the disk header (sector size, geometry, sector count), every "Possible partition" found during the scan with its type, size and sector range, a consistency-check phase, and finally a "Guessed primary partition table" — without modifying the disk. Add `-vv` to also be told why individual guesses were discarded as invalid.

### Scan a raw disk image

Run the same guess pass against a file copy of the disk.

```
gpart -vv -s 4096 disk.dd
```

Outcome: treats the image file as the disk and prints the same guessed table; `-s` overrides sector-size probing, which is needed for images of advanced-format (4 Kn) disks where autodetection may guess wrong.

### List the filesystem guessing modules

See which signatures this build can recognize before scanning.

```
gpart -L
```

Outcome: prints every built-in guessing module with its weight and exits — the module names are also the arguments accepted by `-w` to bias or suppress individual guessers during a scan.

### Write the guessed table back

The actual recovery step, after reviewing a clean guess.

```
gpart -b mbr.backup -W /dev/sdb /dev/sdb
```

Outcome: scans the disk, saves the current MBR to `mbr.backup` (`-b`), and — only if the check phase reports zero inconsistencies — writes the guessed primary partition table to the device named after `-W`; the `-W` argument may instead be a new file, which reconstructs an MBR image without touching the disk.

*No corpus evidence — derived from upstream documentation.*
