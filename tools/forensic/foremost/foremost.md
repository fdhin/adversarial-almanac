---
name: "foremost"
categories:
  - forensics
version: "1.5.7-7"
homepage: "http://foremost.sourceforge.net/"
origin: "arch"
arch-repo: "extra"
packaged-by: [arch, kali, nixpkgs]
arch-builddate: "2024-07-12"
build-system: make
license: custom
meta-checked: "2026-11-09"
github-repo: "korczis/foremost"
github-stars: 381
github-pushed: "2023-05-26"
github-archived: false
github-issues: 7
github-match: "search"
gh-checked: "2026-11-07"
worth: 3.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# foremost

> Carves files out of disk images or raw devices by matching known headers, footers and data structures.

## What it's for

foremost recovers files from evidence sources based on their content rather than the file system: it scans a disk image (dd, Safeback, EnCase, ...) or a raw device for headers and footers of known formats and extracts the matches into an output directory, together with an audit file documenting what was carved. It works where the file system is damaged, reformatted or absent, and the set of recognized formats is configurable through `foremost.conf` — built-in types include jpg, png, gif, bmp, avi, mov, mp4, wmv, pdf, ole (Office documents), zip, rar, htm and exe. `-q` quick mode trades thoroughness for speed and should be avoided on NTFS images (small files stored in the MFT get missed); `-a` disables error checking to also extract corrupted fragments. As a carving tool it does not preserve file names or metadata — those are gone with the file system.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Carve all supported types from an image

```
foremost -i disk.dd -o /mnt/carved
```

Outcome: scans the image for every built-in file type (default `-t all`), writes recovered files into per-type subdirectories of `/mnt/carved` (jpg/, pdf/, ole/, ...) and records everything found in `/mnt/carved/audit.txt`. The output directory must not already exist.

### Carve only specific file types

```
foremost -t jpg,pdf -i disk.dd -o /mnt/carved
```

Outcome: restricts the search to the listed comma-separated types, which runs faster and produces a much smaller output set when only certain formats matter.

### Audit an image without extracting anything

```
foremost -w -i disk.dd -o /mnt/carved
```

Outcome: writes only the audit file listing the offsets and types of everything that would be carved — a quick triage of an image before committing to a full extraction.

### Carve with a custom header/footer configuration

```
foremost -c custom.conf -i disk.dd -o /mnt/carved
```

Outcome: uses the given configuration file instead of `/etc/foremost.conf`, so a custom format (own magic bytes, footer, case sensitivity and maximum size) gets carved by its header/footer definition. Skip the `-c` flag to use a `foremost.conf` in the current directory.

### Skip processed regions and carve from a live device

```
foremost -s 4096 -t ole -i /dev/sdb1 -o /mnt/carved
```

Outcome: skips the first 4096 blocks of the input before scanning (e.g. past a known-clean region), carving OLE/Office documents from a raw block device rather than an image file.
