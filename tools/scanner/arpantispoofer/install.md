---
tool: "arpantispoofer"
build-system: "scripts"
upstream: "http://sourceforge.net/projects/arpantispoofer/"
recipe: "pentoo-ebuild"
version: "1.0.1.32"
license: "GPL-3"
---

# Installing arpantispoofer

> An utility to detect and resist arp spoofing

Recipe source of truth: Pentoo [`arpantispoofer.ebuild`](https://github.com/pentoo/pentoo/tree/master/portage/trunk/net-analyzer/arpantispoofer) (version `1.0.1.32`).

## Gentoo / Pentoo

```bash
sudo eselect repository add pentoo git https://github.com/pentoo/pentoo.git
sudo emerge --ask arpantispoofer
```

## Arch / BlackArch

Not packaged — build from source below (AUR unlikely; check `paru -Ss arpantispoofer`).

## Any other Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://sourceforge.net/projects/arpantispoofer/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from ebuild RDEPEND/DEPEND)

- Gentoo packages: `gnome-base/libgnomeui`

Backlinks: [[scanner]] · [[arpantispoofer]] · [[Pentoo Gap Analysis]]
