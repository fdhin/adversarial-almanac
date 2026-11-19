---
tool: "plaintoo"
build-system: "scripts"
upstream: "http://www.plain-text.info/"
recipe: "pentoo-ebuild"
version: "0.1"
license: "GPL-2"
---

# Installing plaintoo

> Plaintoo is a pygtk frontend to bkhive and samdump2 (included)

Recipe source of truth: Pentoo [`plaintoo.ebuild`](https://github.com/pentoo/pentoo/tree/master/portage/trunk/app-crypt/plaintoo) (version `0.1`).

## Gentoo / Pentoo

```bash
sudo eselect repository add pentoo git https://github.com/pentoo/pentoo.git
sudo emerge --ask plaintoo
```

## Arch / BlackArch

Not packaged — build from source below (AUR unlikely; check `paru -Ss plaintoo`).

## Any other Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://www.plain-text.info/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

Backlinks: [[cracker]] · [[plaintoo]] · [[Pentoo Gap Analysis]]
