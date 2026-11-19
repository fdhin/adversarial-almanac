---
tool: "dpapick"
build-system: "python"
upstream: "http://www.dpapick.com"
recipe: "pentoo-ebuild"
version: "0.3"
license: "GPL-3"
---

# Installing dpapick

> Open-source tool that allows decryption of DPAPI structures in an offline way

Recipe source of truth: Pentoo [`dpapick.ebuild`](https://github.com/pentoo/pentoo/tree/master/portage/trunk/app-forensics/dpapick) (version `0.3`).

## Gentoo / Pentoo

```bash
sudo eselect repository add pentoo git https://github.com/pentoo/pentoo.git
sudo emerge --ask dpapick
```

## Arch / BlackArch

Not packaged — build from source below (AUR unlikely; check `paru -Ss dpapick`).

## Any other Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://www.dpapick.com) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from ebuild RDEPEND/DEPEND)

- Python (pip): `m2crypto`, `pyasn1`, `python-registry`

Backlinks: [[forensic]] · [[dpapick]] · [[Pentoo Gap Analysis]]
