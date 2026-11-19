---
name: "bmap-tools"
categories:
  - blackarch-forensic
version: "3.7"
homepage: "https://github.com/yoctoproject/bmaptool"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: GPL2
meta-checked: "2026-11-09"
github-repo: "yoctoproject/bmaptool"
github-stars: 98
github-pushed: "2026-06-05"
github-archived: false
github-issues: 32
github-match: "exact"
gh-checked: "2026-11-09"
worth: 6.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 25
gh-activity-checked: "2026-11-09"
---

# bmap-tools

> Copy or flash large sparse disk images faster and more safely than dd, using a block map.

## What it's for

`bmaptool` creates and consumes block maps (bmap files) — XML descriptions
of which blocks of an image actually contain data. Because raw system
images are usually sparse, flashing with a bmap writes only the mapped
blocks, skipping holes, which is typically several times faster than `dd`
while still verifying SHA256 checksums of every mapped range as it writes.
It was created for the Tizen IVI project for flashing images to USB sticks
and block devices, and is now the standard flashing tool for Yocto and
embedded Linux images. It also refuses to write to mounted block devices,
decompresses `.gz`/`.bz2`/`.xz`/`.zst` images on the fly, and can read the
image directly from a URL. It is a copier/flasher, not an imager or
forensic analyzer: it will not image a device for you, and for write-once
evidence work `dc3dd`/`air` are the right tools. Originally by Artem
Bityutskiy at Intel; the Intel repository is archived and the project is
maintained at the yoctoproject/bmaptool repository.

## Typical use cases

### Generate a block map for an image

Create the bmap file that lets `bmaptool copy` skip empty blocks.

```
bmaptool create -o image.img.bmap image.img
```

Outcome: writes an XML bmap listing all mapped block ranges with SHA256
checksums (printed to stdout if `-o` is omitted).

### Flash an image to a block device with verification

Write only the mapped blocks and verify checksums as you go.

```
bmaptool copy --bmap image.img.bmap image.img /dev/sdX
```

Outcome: copies the image to the device, verifying each mapped range
against the bmap; opens the destination block device exclusively and
refuses to run if it is mounted, so a wrong target fails loudly instead of
silently destroying data.

### Flash directly from a URL without downloading first

Skip the local copy and stream a compressed remote image to the target.

```
bmaptool copy https://example.com/images/board.img.xz /dev/sdX
```

Outcome: fetches the image over HTTP(S), decompresses it on the fly, and
auto-discovers `board.img.bmap` next to it on the server; checksums from
the bmap are verified during the write.

### Copy without a bmap

Fall back to a plain full copy when no bmap exists.

```
bmaptool copy --nobmap image.img /dev/sdX
```

Outcome: copies every block without bmap acceleration or range
verification — roughly `dd` semantics but with bmaptool's mounted-device
and exclusive-open protections.

*No corpus evidence — derived from upstream documentation.*
