---
name: "safecopy"
categories:
  - blackarch-forensic
version: "1.7"
homepage: "https://sourceforge.net/projects/safecopy/files/safecopy/"
packaged-by: [blackarch, kali, aur, nixpkgs]
blackarch-builddate: "2026-09-13"
build-system: autotools
license: GPL-2.0-or-later
meta-checked: "2026-11-09"
worth: 1.5
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# safecopy

> Damaged-media imager that keeps reading where dd, cp and cat give up.

## What it's for

safecopy (by Corvus Corax / Eric Noack) images failing disks, partitions and
optical media. Once a normal copy tool hits an unrecoverable I/O error, further
reads usually fail until the file is reopened — safecopy instead skips damaged
areas, keeps reading after them, incrementally narrows the exact bounds of each
bad area, and supports staged rescue passes from gentle to aggressive (up to
low-level RAW access on CD/DVD drives). It produces an image file plus badblock
lists; it does not repair anything — afterwards, run filesystem tools or carving
against the rescued image.

## Typical use cases

### First, gentle rescue pass

Grab everything readable fast while stressing the media as little as possible.

```
safecopy --stage1 /dev/sr0 rescue.img
```

Outcome: reads the easy data into `rescue.img`, zero-fills unreadable regions,
and records their extents in `stage1.badblocks` for the next pass.

### Second pass pinning down bad-area edges

Recover the good sectors at the fringes of damaged areas.

```
safecopy --stage2 /dev/sr0 rescue.img
```

Outcome: re-reads the source (preset consumes `stage1.badblocks`) hunting for
the exact start and end of each bad area with fine resolution; updates the
image in place and writes `stage2.badblocks`.

### Final, aggressive pass

Last-ditch attempt on what is still missing.

```
safecopy --stage3 /dev/sr0 rescue.img
```

Outcome: maximum retries plus head-realignment tricks and low-level access on
remaining holes; expect noticeably longer runtime and extra strain on the
dying media.

### Match the read blocksize to the media

Avoid read-size mismatches on odd-sector media.

```
safecopy --stage1 -b 2048 /dev/sr0 rescue.img
```

Outcome: read operations use 2048-byte blocks (a CD's physical sector size)
instead of the auto-detected default.

*No corpus evidence — derived from upstream documentation.*
