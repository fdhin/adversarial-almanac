---
tool: "dnsa"
build-system: "scripts"
upstream: "http://packetfactory.openwall.net/projects/dnsa/index.html"
recipe: "pentoo-ebuild"
version: "0.5-r1"
license: "GPL-2"
---

# Installing dnsa

> Swiss-army knife tool for dns auditing

Recipe source of truth: Pentoo [`dnsa.ebuild`](https://github.com/pentoo/pentoo/tree/master/portage/trunk/net-analyzer/dnsa) (version `0.5-r1`).

## Gentoo / Pentoo

```bash
sudo eselect repository add pentoo git https://github.com/pentoo/pentoo.git
sudo emerge --ask dnsa
```

## Arch / BlackArch

Not packaged — build from source below (AUR unlikely; check `paru -Ss dnsa`).

## Any other Linux distro

**From source:**

```bash
curl -fLO http://packetfactory.openwall.net/projects/dnsa/dnsa-current.tar.gz
tar xf dnsa-current.tar.gz
cd dnsa-current
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from ebuild RDEPEND/DEPEND)

- Gentoo packages: `net-libs/libnet`, `net-libs/libpcap`

Backlinks: [[scanner]] · [[dnsa]] · [[Pentoo Gap Analysis]]
