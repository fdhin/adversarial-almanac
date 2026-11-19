---
tool: "bluemaho"
build-system: "python"
upstream: "http://wiki.thc.org/BlueMaho"
recipe: "pentoo-ebuild"
version: "090417-r2"
license: "GPL-2"
---

# Installing bluemaho

> BlueMaho is GUI-shell (interface) for suite of tools for testing security of bluetooth devices

Recipe source of truth: Pentoo [`bluemaho.ebuild`](https://github.com/pentoo/pentoo/tree/master/portage/trunk/net-wireless/bluemaho) (version `090417-r2`).

## Gentoo / Pentoo

```bash
sudo eselect repository add pentoo git https://github.com/pentoo/pentoo.git
sudo emerge --ask bluemaho
```

## Arch / BlackArch

Not packaged — build from source below (AUR unlikely; check `paru -Ss bluemaho`).

## Any other Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://wiki.thc.org/BlueMaho) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from ebuild RDEPEND/DEPEND)

- Python (pip): `wxpython`
- Gentoo packages: `dev-libs/libxml2`, `net-wireless/bluez`, `net-wireless/bt-audit`, `sys-libs/readline`, `virtual/libusb`

Backlinks: [[wireless]] · [[bluemaho]] · [[Pentoo Gap Analysis]]
