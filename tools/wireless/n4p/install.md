---
tool: "n4p"
build-system: "scripts"
upstream: "https://github.com/Cyb3r-Assassin"
recipe: "pentoo-ebuild"
version: "2.2"
license: "GPL-2"
---

# Installing n4p

> Comprehensive Wireless and Network packet based hacking framework.Compare to Metasploit

Recipe source of truth: Pentoo [`n4p.ebuild`](https://github.com/pentoo/pentoo/tree/master/portage/trunk/net-wireless/n4p) (version `2.2`).

## Gentoo / Pentoo

```bash
sudo eselect repository add pentoo git https://github.com/pentoo/pentoo.git
sudo emerge --ask n4p
```

## Arch / BlackArch

Not packaged — build from source below (AUR unlikely; check `paru -Ss n4p`).

## Any other Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/Cyb3r-Assassin) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

Backlinks: [[wireless]] · [[n4p]] · [[Pentoo Gap Analysis]]
