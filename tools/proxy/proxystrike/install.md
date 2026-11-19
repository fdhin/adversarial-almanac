---
tool: "proxystrike"
build-system: "python"
upstream: "http://code.google.com/p/proxystrike/"
recipe: "pentoo-ebuild"
version: "9999"
license: "GPL-2"
---

# Installing proxystrike

> A tool designed to find vulnerabilities while browsing an application

Recipe source of truth: Pentoo [`proxystrike.ebuild`](https://github.com/pentoo/pentoo/tree/master/portage/trunk/net-proxy/proxystrike) (version `9999`).

## Gentoo / Pentoo

```bash
sudo eselect repository add pentoo git https://github.com/pentoo/pentoo.git
sudo emerge --ask proxystrike
```

## Arch / BlackArch

Not packaged — build from source below (AUR unlikely; check `paru -Ss proxystrike`).

## Any other Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://code.google.com/p/proxystrike/) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from ebuild RDEPEND/DEPEND)

- Python (pip): `pyqt4`, `pycurl`, `pyopenssl`, `sip`

Backlinks: [[proxy]] · [[proxystrike]] · [[Pentoo Gap Analysis]]
