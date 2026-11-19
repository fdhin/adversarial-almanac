---
name: "magicrescue"
categories:
  - blackarch-forensic
version: "v1.1.10.r8.gd9a5793"
homepage: "https://github.com/jbj/magicrescue"
packaged-by: [blackarch, kali, aur, nixpkgs]
blackarch-builddate: "2025-12-19"
build-system: autotools
license: GPL-1.0-or-later
meta-checked: "2026-11-09"
github-repo: "jbj/magicrescue"
github-stars: 10
github-pushed: "2021-09-12"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# magicrescue

> Content-based file carver that scans block devices for magic bytes and extracts known file types via recipes.

## What it's for

Magic Rescue scans a block device or image for file signatures ("magic bytes") and carves matching files out by invoking an external extractor program — it never reads file system structures, so it works both as an undelete utility and on corrupted drives or partitions of any file system. Carving behavior is defined by recipe files (packaged under /usr/share/magicrescue/recipes; the set covers jpeg-jfif, jpeg-exif, png, zip, gzip, rar, mp3-id3v1/v2, elf, sqlite, msoffice, canon-cr2, nikon-raw, flac, avi, flv, mbox and more). Limits: on fragmented file systems only the first contiguous chunk of each file is recovered, and the project is no longer actively developed. Upstream explicitly warns to run it sandboxed — recipes execute external programs on arbitrary untrusted data. Companion utilities: dupemap (deduplication database) and magicsort.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Carve JPEG files from a partition

```
magicrescue -r jpeg-jfif -d recovered/ /dev/sdb1
```

Outcome: scans the device and writes each recovered JPEG into recovered/ (one file per hit); -d (output directory) and -r (recipe) are mandatory. Do not place the output directory on the device being rescued.

### Carve every supported file type at once

Pass the whole recipe directory.

```
magicrescue -r /usr/share/magicrescue/recipes -d recovered/ /dev/sdb1
```

Outcome: applies every installed recipe in turn and recovers all supported file types — ensure ample free space, since common types such as JPEG and gzip produce large volumes of output.

### Faster scan assuming block-aligned files

```
magicrescue -b 512 -r jpeg-jfif -d recovered/ /dev/sdb1
```

Outcome: only considers file starts at 512-byte boundaries — usually much faster, but skips files with leading garbage, files embedded inside other files, and small files on file systems that do not align them to block boundaries.

### Carve from a set of image files

```
magicrescue -I imagelist.txt -r jpeg-jfif -d recovered/
```

Outcome: carves from each file named in imagelist.txt (one filename per line) instead of a block device — useful for split or loopback image sets.
