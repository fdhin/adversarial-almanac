---
tool: "nmap_vulscan"
build-system: "scripts"
upstream: "http://www.scip.ch/"
recipe: "pentoo-ebuild"
version: "1.0-r1"
license: "BSD"
---

# Installing nmap_vulscan

> A NSE vulnerability scanner which uses an offline version of different vulnerability databases

Recipe source of truth: Pentoo [`nmap_vulscan.ebuild`](https://github.com/pentoo/pentoo/tree/master/portage/trunk/net-analyzer/nmap_vulscan) (version `1.0-r1`).

## Gentoo / Pentoo

```bash
sudo eselect repository add pentoo git https://github.com/pentoo/pentoo.git
sudo emerge --ask nmap_vulscan
```

## Arch / BlackArch

Not packaged — build from source below (AUR unlikely; check `paru -Ss nmap_vulscan`).

## Any other Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://www.scip.ch/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from ebuild RDEPEND/DEPEND)

- Gentoo packages: `net-analyzer/nmap`

Backlinks: [[scanner]] · [[nmap_vulscan]] · [[Pentoo Gap Analysis]]
