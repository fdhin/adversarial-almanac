---
tool: "horst"
build-system: "scripts"
upstream: "http://br1.einfach.org/tech/horst/"
recipe: "pentoo-ebuild"
nix-package: "horst"
aur-package: "horst"
version: "3.0"
license: "GPL-2"
---

# Installing horst

> Small 802.11 wireless LAN analyzer

Recipe source of truth: Pentoo [`horst.ebuild`](https://github.com/pentoo/pentoo/tree/master/portage/trunk/net-wireless/horst) (version `3.0`).

## Gentoo / Pentoo

```bash
sudo eselect repository add pentoo git https://github.com/pentoo/pentoo.git
sudo emerge --ask horst
```

## Arch / BlackArch

Not packaged — build from source below (AUR unlikely; check `paru -Ss horst`).

## Nix (works on any distro)

```bash
nix run nixpkgs#horst    # ad-hoc run, no install
nix profile install nixpkgs#horst   # or persist it
```

## Any other Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://br1.einfach.org/tech/horst/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from ebuild RDEPEND/DEPEND)

- Gentoo packages: `net-libs/libpcap`, `sys-libs/ncurses`

Backlinks: [[wireless]] · [[horst]] · [[Pentoo Gap Analysis]]
