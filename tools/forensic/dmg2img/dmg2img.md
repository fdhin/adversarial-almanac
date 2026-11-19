---
name: "dmg2img"
categories:
  - blackarch-forensic
version: "1.6.7"
homepage: "http://vu1tur.eu.org/tools/"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2024-12-06"
build-system: make
license: GPL2
meta-checked: "2026-11-09"
github-repo: "Lekensteyn/dmg2img"
github-stars: 272
github-pushed: "2020-12-27"
github-archived: false
github-issues: 16
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# dmg2img

> Converts Apple UDIF compressed DMG images to raw HFS+/GPT disk images that can be loop-mounted on Linux.

## What it's for

dmg2img unpacks Apple's compressed (UDIF) DMG disk images into standard raw image files, handling zlib, bzip2, and ADC compression. The result is a plain `.img` that Linux can loop-mount and other forensic tools can process — the offline equivalent of `hdiutil convert <input.dmg> -format UDTO` on macOS. The distribution also includes vfdecrypt for encrypted DMG files. It converts images only; it does not create them.

## Typical use cases

### Convert a compressed DMG to a raw image

```
dmg2img -i installer.dmg installer.img
```

Outcome: decompresses every UDIF block and writes the raw image; the positional form `dmg2img installer.dmg installer.img` does the same, with progress printed as blocks are processed.

### List partitions inside a DMG

```
dmg2img -l installer.dmg
```

Outcome: prints the partition table found in the DMG without converting anything — confirms the layout and partition numbers before extracting.

### Extract a single partition

```
dmg2img -p 2 installer.dmg partition.img
```

Outcome: converts only partition number 2 from the DMG's partition list and skips the rest, pulling one volume out of a multi-partition image.

### Loop-mount the converted image

```
mount -t hfsplus -o loop installer.img /mnt
```

Outcome: exposes the HFS+ volume through /mnt for browsing as root. GPT-partitioned images are detected and their partition layout reported, so multi-partition results are mounted with the appropriate partition offset instead of the whole image.

*No corpus evidence — derived from upstream documentation.*
