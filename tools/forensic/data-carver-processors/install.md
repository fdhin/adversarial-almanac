---
tool: "data-carver-processors"
build-system: "scripts"
upstream: "http://www.citadelsystems.net/index.php/forensics-tools/34-data-carver/56-data-carver-processors"
recipe: "pentoo-ebuild"
version: "0"
license: "GPL-2"
---

# Installing data-carver-processors

> Data carving analysis for JPEG, PDF and Videos

Recipe source of truth: Pentoo [`data-carver-processors.ebuild`](https://github.com/pentoo/pentoo/tree/master/portage/trunk/app-forensics/data-carver-processors) (version `0`).

## Gentoo / Pentoo

```bash
sudo eselect repository add pentoo git https://github.com/pentoo/pentoo.git
sudo emerge --ask data-carver-processors
```

## Arch / BlackArch

Not packaged — build from source below (AUR unlikely; check `paru -Ss data-carver-processors`).

## Any other Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://www.citadelsystems.net/index.php/forensics-tools/34-data-carver/56-data-carver-processors) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from ebuild RDEPEND/DEPEND)

- Gentoo packages: `app-forensics/stegdetect`, `media-gfx/imagemagick`, `media-libs/exiftool`, `media-video/mplayer`, `sys-apps/file`

Backlinks: [[forensic]] · [[data-carver-processors]] · [[Pentoo Gap Analysis]]
