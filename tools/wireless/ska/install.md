---
tool: "ska"
build-system: "scripts"
upstream: "http://homepages.tu-darmstadt.de/~p_larbig/wlan/"
recipe: "pentoo-ebuild"
version: "0.2"
license: "GPL-2"
---

# Installing ska

> Fake Shared Key Authentication

Recipe source of truth: Pentoo [`ska.ebuild`](https://github.com/pentoo/pentoo/tree/master/portage/trunk/net-wireless/ska) (version `0.2`).

## Gentoo / Pentoo

```bash
sudo eselect repository add pentoo git https://github.com/pentoo/pentoo.git
sudo emerge --ask ska
```

## Arch / BlackArch

Not packaged — build from source below (AUR unlikely; check `paru -Ss ska`).

## Any other Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://homepages.tu-darmstadt.de/~p_larbig/wlan/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

Backlinks: [[wireless]] · [[ska]] · [[Pentoo Gap Analysis]]
