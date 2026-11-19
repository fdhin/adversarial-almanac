---
tool: "rpcapd"
build-system: "scripts"
upstream: "http://www.winpcap.org/devel.htm"
recipe: "pentoo-ebuild"
aur-package: "rpcapd"
version: "4.1.2"
license: "unknown"
---

# Installing rpcapd

> Remote packet capture daemon

Recipe source of truth: Pentoo [`rpcapd.ebuild`](https://github.com/pentoo/pentoo/tree/master/portage/trunk/net-analyzer/rpcapd) (version `4.1.2`).

## Gentoo / Pentoo

```bash
sudo eselect repository add pentoo git https://github.com/pentoo/pentoo.git
sudo emerge --ask rpcapd
```

## Arch / BlackArch

Not packaged — build from source below (AUR unlikely; check `paru -Ss rpcapd`).

## Any other Linux distro

**From source:**

```bash
curl -fLO http://www.winpcap.org/install/bin/WpcapSrc_4_1_2.zip
tar xf WpcapSrc_4_1_2.zip
cd WpcapSrc_4_1_2
# run scripts directly (see recipe for interpreter and layout)
```

Backlinks: [[scanner]] · [[rpcapd]] · [[Pentoo Gap Analysis]]
