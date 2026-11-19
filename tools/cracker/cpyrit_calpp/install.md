---
tool: "cpyrit_calpp"
build-system: "python"
upstream: "http://code.google.com/p/pyrit/"
recipe: "pentoo-ebuild"
version: "9999"
license: "GPL-3"
---

# Installing cpyrit_calpp

> A GPU-based WPA-PSK and WPA2-PSK cracking tool

Recipe source of truth: Pentoo [`cpyrit_calpp.ebuild`](https://github.com/pentoo/pentoo/tree/master/portage/trunk/app-crypt/cpyrit_calpp) (version `9999`).

## Gentoo / Pentoo

```bash
sudo eselect repository add pentoo git https://github.com/pentoo/pentoo.git
sudo emerge --ask cpyrit_calpp
```

## Arch / BlackArch

Not packaged — build from source below (AUR unlikely; check `paru -Ss cpyrit_calpp`).

## Any other Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://code.google.com/p/pyrit/) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from ebuild RDEPEND/DEPEND)

- Gentoo packages: `dev-util/calpp-0.87`, `x11-drivers/ati-drivers-10.2`

Backlinks: [[cracker]] · [[cpyrit_calpp]] · [[Pentoo Gap Analysis]]
