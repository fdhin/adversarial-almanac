---
tool: "grimwepa"
build-system: "scripts"
upstream: "http://code.google.com/p/grimwepa/"
recipe: "pentoo-ebuild"
version: "1.10_p5-r100"
license: "GPL-3"
---

# Installing grimwepa

> A password cracker for both WEP and WPA-encrypted access points

Recipe source of truth: Pentoo [`grimwepa.ebuild`](https://github.com/pentoo/pentoo/tree/master/portage/trunk/net-wireless/grimwepa) (version `1.10_p5-r100`).

## Gentoo / Pentoo

```bash
sudo eselect repository add pentoo git https://github.com/pentoo/pentoo.git
sudo emerge --ask grimwepa
```

## Arch / BlackArch

Not packaged — build from source below (AUR unlikely; check `paru -Ss grimwepa`).

## Any other Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://code.google.com/p/grimwepa/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from ebuild RDEPEND/DEPEND)

- Gentoo packages: `app-crypt/crunch`, `app-crypt/pyrit`, `net-analyzer/hydra`, `net-analyzer/macchanger`, `net-analyzer/wireshark`, `net-wireless/wpa_supplicant`, `virtual/jre-1.5`

Backlinks: [[wireless]] · [[grimwepa]] · [[Pentoo Gap Analysis]]
