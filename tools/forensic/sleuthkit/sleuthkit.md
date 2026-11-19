---
name: "sleuthkit"
categories:
  - forensics
version: "4.15.0-1"
homepage: "https://www.sleuthkit.org/sleuthkit"
origin: "arch"
arch-repo: "extra"
packaged-by: [arch, kali, nixpkgs]
arch-builddate: "2026-04-19"
build-system: perl
license: CPL-1.0, GPL-2.0-or-later, IPL-1.0
meta-checked: "2026-11-09"
github-repo: "sleuthkit/sleuthkit"
github-stars: 3185
github-pushed: "2026-10-27"
github-archived: false
github-issues: 497
github-match: "search"
gh-checked: "2026-11-07"
worth: 8.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 321
gh-activity-checked: "2026-11-09"
---

# sleuthkit

> Classic command-line forensic toolkit for disk images and file systems (mmls, fls, icat, blkls).

## What it's for

The Sleuth Kit (TSK) is a suite of low-level, single-purpose command-line tools that analyze raw disk images (dd-style) and the file systems inside them — NTFS, FAT/exFAT, ext2/3/4, FFS/UFS, and more. Each tool targets one layer of a volume: `mmls` reads the partition layout, `fls` lists file and directory names (including deleted ones), `icat` extracts content by metadata address, `blkls` dumps unallocated space for carving. It is the engine underneath Autopsy and a standard building block for Unix-side disk forensics pipelines. It does not acquire images (pair it with dd or similar) and ships no GUI of its own. Upstream recommends verifying any finding with a second tool.

## Typical use cases

### Map the partitions in a disk image

Get the volume system layout before touching any file system.

```
mmls disk.img
```

Outcome: prints the partition table with start/end offsets and length for every slot, allocated and unallocated. Note the start offset of the file system partition you care about (e.g. 2048) and pass it to the file system tools via `-o`.

### List files, including deleted ones

Enumerate names at the human interface layer of one file system.

```
fls -o 2048 -r -p disk.img
```

Outcome: recursively (`-r`) lists file and directory names in the file system starting at offset 2048, printing full paths (`-p`) and marking deleted entries with `*`. Drop `-r` for the root directory only, add `-d` for deleted entries only, or `-l` for ls-style long details.

### Extract file content by metadata address

Pull the data of a specific file straight out of the image.

```
icat -o 2048 disk.img 42 > file.bin
```

Outcome: writes the content allocated to metadata entry (inode) 42 to file.bin; take the address from `fls -l` output. `-r` attempts recovery of a deleted entry, `-R` also suppresses recovery errors, and `-s` appends the file's slack space.

### Dump unallocated space for carving

Collect everything the file system no longer references.

```
blkls -o 2048 disk.img > unalloc.bin
```

Outcome: writes a contiguous stream of all unallocated blocks/clusters of the file system to unalloc.bin. Run strings on it or feed it to a carver to recover deleted content; `-e` includes every block instead, `-a`/`-A` select allocated/unallocated explicitly.

### Generate a timeline body file

Produce mactime-body output covering allocated and deleted names.

```
fls -o 2048 -r -m / disk.img > bodyfile
```

Outcome: emits every file name (deleted included) in mactime input format with `/` as the mount-point prefix; the bodyfile feeds the `mactime` program to build a sorted activity timeline.

*No corpus evidence — derived from upstream documentation.*
