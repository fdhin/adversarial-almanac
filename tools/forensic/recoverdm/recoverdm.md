---
name: "recoverdm"
categories:
  - blackarch-forensic
version: "0.20"
homepage: "http://www.vanheusden.com/recoverdm/"
packaged-by: [blackarch, kali, aur, nixpkgs]
blackarch-builddate: "2023-07-08"
build-system: make
license: GPL
meta-checked: "2026-11-09"
github-repo: "D3vil0p3r/recoverdm"
github-stars: 5
github-pushed: "2024-02-18"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# recoverdm

> Sector-level data recovery for media with bad sectors — reads past read errors (RAW mode for CDs/DVDs) and, with mergebad, recombines multiple damaged copies of the same media.

## What it's for

recoverdm (by Folkert van Heusden) recovers files and whole devices from damaged media: it reads sector by sector, retries on errors, falls back to RAW-mode reads when a CD/DVD sector fails normal mode, and writes an empty sector to the output where recovery is impossible, so the image stays positionally intact. It records the offsets and sizes of unrecoverable blocks in a bad-blocks list, which the bundled mergebad utility then uses to merge multiple damaged images of the same media into one best-effort image. Use it when dd fails on a scratchy disc or a dying disk and you want maximum extraction rather than a byte-perfect (impossible) copy; it is a recovery tool, not an imaging/wipe tool for healthy media.

## Typical use cases

### Image a disk with bad sectors

Recover a dying disk to an image file.

```
recoverdm -t 40 -i /dev/sdb -o sdb.img -l sdb.badblocks
```

Outcome: writes a sector-for-sector image to sdb.img (which must not already exist), substituting empty sectors where reads fail after 6 retries; sdb.badblocks lists the offset and size of each unrecoverable block for later mergebad runs.

### Recover a single damaged file

Salvage one readable-but-failing file.

```
recoverdm -t 1 -i damaged.doc -o recovered.doc
```

Outcome: copies the file sector by sector with retries, producing recovered.doc with zeros only where sectors were beyond recovery — `-t 1` selects file mode (10 is floppy, 40 IDE disk).

### Merge images from multiple damaged copies

Combine several partial recoveries of the same disc.

```
mergebad -o merged.iso -i cd1.badblocks cd1.iso -i cd2.badblocks cd2.iso
```

Outcome: writes merged.iso taking each sector from whichever copy read it successfully; only sectors bad in every input end up empty, and `-l leftover.badblocks` records those final gaps.

*No corpus evidence — derived from upstream documentation.*
