---
tool: "videojak"
build-system: "scripts"
upstream: "http://videojak.sourceforge.net/"
recipe: "pentoo-ebuild"
version: "2.0"
license: "GPL-2"
---

# Installing videojak

> VideoJak is an IP Video security assessment tool

Recipe source of truth: Pentoo [`videojak.ebuild`](https://github.com/pentoo/pentoo/tree/master/portage/trunk/net-analyzer/videojak) (version `2.0`).

## Gentoo / Pentoo

```bash
sudo eselect repository add pentoo git https://github.com/pentoo/pentoo.git
sudo emerge --ask videojak
```

## Arch / BlackArch

Not packaged — build from source below (AUR unlikely; check `paru -Ss videojak`).

## Any other Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://videojak.sourceforge.net/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from ebuild RDEPEND/DEPEND)

- Gentoo packages: `net-analyzer/ettercap`

Backlinks: [[scanner]] · [[videojak]] · [[Pentoo Gap Analysis]]
