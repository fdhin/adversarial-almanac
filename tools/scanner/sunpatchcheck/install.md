---
tool: "sunpatchcheck"
build-system: "scripts"
upstream: "http://www.titania.co.uk/"
recipe: "pentoo-ebuild"
version: "3.3"
license: "GPL-2"
---

# Installing sunpatchcheck

> Sun Missing Patch checker

Recipe source of truth: Pentoo [`sunpatchcheck.ebuild`](https://github.com/pentoo/pentoo/tree/master/portage/trunk/app-admin/sunpatchcheck) (version `3.3`).

## Gentoo / Pentoo

```bash
sudo eselect repository add pentoo git https://github.com/pentoo/pentoo.git
sudo emerge --ask sunpatchcheck
```

## Arch / BlackArch

Not packaged — build from source below (AUR unlikely; check `paru -Ss sunpatchcheck`).

## Any other Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://www.titania.co.uk/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

Backlinks: [[scanner]] · [[sunpatchcheck]] · [[Pentoo Gap Analysis]]
