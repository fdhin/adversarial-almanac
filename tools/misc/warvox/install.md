---
tool: "warvox"
build-system: "python"
upstream: "http://warvox.org"
recipe: "pentoo-ebuild"
version: "9999"
license: "BSD"
---

# Installing warvox

> VoIP war dialing suite of tools

Recipe source of truth: Pentoo [`warvox.ebuild`](https://github.com/pentoo/pentoo/tree/master/portage/trunk/net-misc/warvox) (version `9999`).

## Gentoo / Pentoo

```bash
sudo eselect repository add pentoo git https://github.com/pentoo/pentoo.git
sudo emerge --ask warvox
```

## Arch / BlackArch

Not packaged — build from source below (AUR unlikely; check `paru -Ss warvox`).

## Any other Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://warvox.org) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from ebuild RDEPEND/DEPEND)

- Python (pip): `gnuplot-py`
- Ruby (gem): `abstract`, `activesupport`, `bundler`, `mail`, `pg`, `rails`, `rake`, `sqlite3`, `will_paginate`
- Gentoo packages: `media-sound/lame`, `media-sound/sox`, `www-servers/mongrel`

Backlinks: [[misc]] · [[warvox]] · [[Pentoo Gap Analysis]]
