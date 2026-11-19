---
name: "recoverjpeg"
categories:
  - blackarch-forensic
version: "2.6.3"
homepage: "https://github.com/samueltardieu/recoverjpeg"
packaged-by: [blackarch, kali, aur, nixpkgs]
blackarch-builddate: "2026-01-13"
build-system: autotools
license: GPL
meta-checked: "2026-11-09"
github-repo: "samueltardieu/recoverjpeg"
github-stars: 80
github-pushed: "2023-01-29"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# recoverjpeg

> Carve JPEG pictures (and MOV movies) out of damaged or repartitioned storage without using the filesystem.

## What it's for

recoverjpeg scans a raw device or disk image for embedded JPEG structures at 512-byte boundaries and copies every salvageable picture out as sequentially numbered files, ignoring the filesystem entirely. Reach for it after accidental repartitioning or formatting, deletion, or filesystem corruption on cameras, memory cards, and hard disks — the authors built it after exactly such a mishap. It is not a filesystem parser and not a complete JPEG validator: some recovered pictures may be structurally valid but garbled, so triage the results afterwards (the bundled sort-pictures and remove-duplicates scripts help). The same package ships recovermov, which carves QuickTime/MOV movies the same way. On physically damaged media, first make a copy with `dd conv=noerror` or ddrescue and run the carver on the image.

## Typical use cases

### Recover pictures from a memory card

Run the carver against the raw device, writing results elsewhere.

```
recoverjpeg -o /mnt/recovery/jpeg /dev/sdc
```

Outcome: scans the card at block boundaries and writes every JPEG found as `imageNNNNN.jpg` under the output directory, with a progress bar reporting how much was analyzed and how many pictures were recovered.

### Skip thumbnails and tiny fragments

Raise the cutoff size so only real pictures are kept.

```
recoverjpeg -s 1m -o /mnt/recovery/jpeg /dev/sdc
```

Outcome: files smaller than 1 MiB are discarded during carving, so output contains only pictures above the threshold instead of thumbnails and fragments.

### Carve a filesystem that does not align files to 512 bytes

Some filesystems (ReiserFS, UFS) aggregate small files off block boundaries; scan every byte.

```
recoverjpeg -b 1 -o /mnt/recovery/jpeg /dev/sdb1
```

Outcome: a much slower scan that starts at every byte offset, maximizing the chance of finding small files; recovered pictures land in the output directory as usual.

### Resume an interrupted carving session

Skip already-analyzed bytes and continue the image numbering where the previous run stopped.

```
recoverjpeg -S 4g -i 1200 -o /mnt/recovery/jpeg disk.img
```

Outcome: the first 4 GiB of the image are skipped and numbering restarts at 1200, so the new files do not overwrite earlier ones and no time is re-spent on the scanned region.

### Recover movies instead of pictures

Use the sibling carver from the same package.

```
recovermov -o /mnt/recovery/mov /dev/sdc
```

Outcome: MOV structures are carved into `video_NNNNN.mov` files in the output directory, using the same boundary-scanning approach.

*No corpus evidence — derived from upstream documentation.*
