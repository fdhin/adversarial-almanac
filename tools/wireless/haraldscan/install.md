---
tool: "haraldscan"
build-system: "python"
upstream: "http://code.google.com/p/haraldscan/"
recipe: "pentoo-ebuild"
version: "0.41"
license: "GPL-3"
---

# Installing haraldscan

> A Bluetooth discovery scanner

Recipe source of truth: Pentoo [`haraldscan.ebuild`](https://github.com/pentoo/pentoo/tree/master/portage/trunk/net-wireless/haraldscan) (version `0.41`).

## Gentoo / Pentoo

```bash
sudo eselect repository add pentoo git https://github.com/pentoo/pentoo.git
sudo emerge --ask haraldscan
```

## Arch / BlackArch

Not packaged — build from source below (AUR unlikely; check `paru -Ss haraldscan`).

## Any other Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://code.google.com/p/haraldscan/) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from ebuild RDEPEND/DEPEND)

- Python (pip): `pybluez`

Backlinks: [[wireless]] · [[haraldscan]] · [[Pentoo Gap Analysis]]
