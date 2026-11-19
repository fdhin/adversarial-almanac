---
name: "myrescue"
categories:
  - blackarch-forensic
version: "0.9.8"
homepage: "http://myrescue.sourceforge.net"
packaged-by: [blackarch, kali, aur, nixpkgs]
blackarch-builddate: "2023-07-08"
build-system: make
license: GPL
meta-checked: "2026-11-09"
github-repo: "montaguethomas/myrescue"
github-stars: 0
github-pushed: "2011-03-11"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# myrescue

> Multi-pass hard-disk rescue tool that copies undamaged blocks first and tracks progress in a block bitmap.

## What it's for

myrescue (by Kristof Koehler and Peter Schlaile) copies a failing disk block-by-block to an image file while keeping a bitmap of every block's state — copied, copied-slowly, unhandled, or N failed attempts. Because subsequent passes re-read only what is still missing, you can run it repeatedly: grab everything readable first, then retry damaged regions. Its distinguishing feature is skip mode: on a read error it exponentially increases the step size to escape the defect quickly, since continuously reading a damaged surface can destroy heads and remaining data. The bitmap feeds two companions shipped in the same package: `myrescue-stat` (block-state statistics) and `myrescue-bitmap2ppm` (damage visualization). It is a last-resort tool for when professional data recovery is not an option — the authors warn it may further damage the disk.

## Typical use cases

### First pass: copy everything readable, skipping defects

Rescue the healthy parts of a dying disk before they degrade further.

```
sudo myrescue -S -B rescue.bitmap /dev/sdX rescue.img
```

Outcome: copies readable blocks from `/dev/sdX` into `rescue.img`, recording per-block state in `rescue.bitmap`; when a read fails, skip mode (`-S`) jumps ahead exponentially instead of hammering the defect.

### Later passes: retry only the failed blocks

Converge toward a complete image across multiple runs.

```
sudo myrescue -B rescue.bitmap -r 3 /dev/sdX rescue.img
```

Outcome: re-reads only blocks still marked unhandled or failed in the bitmap, attempting each up to 3 times before giving up for this run; the bitmap is updated so the next pass resumes where this one stopped.

### Report recovery progress

Quantify how much of the disk has been rescued.

```
myrescue-stat rescue.bitmap
```

Outcome: prints the count and percentage of blocks per state (unhandled, copied OK, copied but slow, failed N times) so you can judge whether another pass is worth the risk to the drive.

### Visualize the damage pattern

Turn the bitmap into an image to see how defects are distributed.

```
myrescue-bitmap2ppm rescue.bitmap 1024 > rescue-map.ppm
```

Outcome: writes a PPM image to stdout — green blocks copied fine, yellow-green blocks read slowly, other colors mark failures; convert with ImageMagick or netpbm to view the disk's physical damage layout.

*No corpus evidence — derived from upstream documentation.*
