---
tool: "post-it-dos"
build-system: "python"
upstream: "http://code.google.com/p/post-it-dos/"
recipe: "pentoo-ebuild"
version: "9999"
license: "GPL-3"
---

# Installing post-it-dos

> A simple piece of PoC code written to demonstrate HTTP POST Denial of Service vulnerabilies

Recipe source of truth: Pentoo [`post-it-dos.ebuild`](https://github.com/pentoo/pentoo/tree/master/portage/trunk/net-analyzer/post-it-dos) (version `9999`).

## Gentoo / Pentoo

```bash
sudo eselect repository add pentoo git https://github.com/pentoo/pentoo.git
sudo emerge --ask post-it-dos
```

## Arch / BlackArch

Not packaged — build from source below (AUR unlikely; check `paru -Ss post-it-dos`).

## Any other Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://code.google.com/p/post-it-dos/) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

Backlinks: [[scanner]] · [[post-it-dos]] · [[Pentoo Gap Analysis]]
