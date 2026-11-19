---
tool: "karmetasploit"
build-system: "scripts"
upstream: "http://dev.metasploit.com/redmine/projects/framework/wiki/Karmetasploit"
recipe: "pentoo-ebuild"
version: "0.1-r3"
license: "GPL-2"
---

# Installing karmetasploit

> KARMA + Metasploit 3 == Karmetasploit

Recipe source of truth: Pentoo [`karmetasploit.ebuild`](https://github.com/pentoo/pentoo/tree/master/portage/trunk/net-wireless/karmetasploit) (version `0.1-r3`).

## Gentoo / Pentoo

```bash
sudo eselect repository add pentoo git https://github.com/pentoo/pentoo.git
sudo emerge --ask karmetasploit
```

## Arch / BlackArch

Not packaged — build from source below (AUR unlikely; check `paru -Ss karmetasploit`).

## Any other Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://dev.metasploit.com/redmine/projects/framework/wiki/Karmetasploit) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from ebuild RDEPEND/DEPEND)

- Gentoo packages: `net-analyzer/metasploit`, `net-misc/dhcp`, `net-wireless/aircrack-ng-1.2_rc1`

Backlinks: [[wireless]] · [[karmetasploit]] · [[Pentoo Gap Analysis]]
