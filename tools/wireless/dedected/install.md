---
tool: "dedected"
build-system: "scripts"
upstream: "https://dedected.org"
recipe: "pentoo-ebuild"
version: "9999"
license: "GPL-2"
---

# Installing dedected

> DECT Sniffer

Recipe source of truth: Pentoo [`dedected.ebuild`](https://github.com/pentoo/pentoo/tree/master/portage/trunk/net-wireless/dedected) (version `9999`).

## Gentoo / Pentoo

```bash
sudo eselect repository add pentoo git https://github.com/pentoo/pentoo.git
sudo emerge --ask dedected
```

## Arch / BlackArch

Not packaged — build from source below (AUR unlikely; check `paru -Ss dedected`).

## Any other Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://dedected.org) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

Backlinks: [[wireless]] · [[dedected]] · [[Pentoo Gap Analysis]]
