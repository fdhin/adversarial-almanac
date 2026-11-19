---
tool: "mosref"
build-system: "scripts"
upstream: "http://sourceforge.net/projects/mosref/"
recipe: "pentoo-ebuild"
version: "2.0_beta3"
license: "LGPL-2"
---

# Installing mosref

> A secure remote execution framework using a compact Scheme-influenced VM

Recipe source of truth: Pentoo [`mosref.ebuild`](https://github.com/pentoo/pentoo/tree/master/portage/trunk/net-analyzer/mosref) (version `2.0_beta3`).

## Gentoo / Pentoo

```bash
sudo eselect repository add pentoo git https://github.com/pentoo/pentoo.git
sudo emerge --ask mosref
```

## Arch / BlackArch

Not packaged — build from source below (AUR unlikely; check `paru -Ss mosref`).

## Any other Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://sourceforge.net/projects/mosref/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

Backlinks: [[scanner]] · [[mosref]] · [[Pentoo Gap Analysis]]
