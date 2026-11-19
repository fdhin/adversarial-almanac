---
tool: "smudge"
build-system: "python"
upstream: "http://www.pentoo.ch/"
recipe: "pentoo-ebuild"
version: "0.8-r1"
license: "GPL-2"
---

# Installing smudge

> Smudge is basically a port of SPIKE into python scripts

Recipe source of truth: Pentoo [`smudge.ebuild`](https://github.com/pentoo/pentoo/tree/master/portage/trunk/app-fuzz/smudge) (version `0.8-r1`).

## Gentoo / Pentoo

```bash
sudo eselect repository add pentoo git https://github.com/pentoo/pentoo.git
sudo emerge --ask smudge
```

## Arch / BlackArch

Not packaged — build from source below (AUR unlikely; check `paru -Ss smudge`).

## Any other Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://www.pentoo.ch/) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

Backlinks: [[fuzzer]] · [[smudge]] · [[Pentoo Gap Analysis]]
