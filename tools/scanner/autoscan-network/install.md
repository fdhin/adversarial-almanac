---
tool: "autoscan-network"
build-system: "make"
upstream: "http://autoscan-network.com/"
recipe: "pentoo-ebuild"
version: "1.50"
license: "GPL-2"
---

# Installing autoscan-network

> Utility for network exploration with Samba support.

Recipe source of truth: Pentoo [`autoscan-network.ebuild`](https://github.com/pentoo/pentoo/tree/master/portage/trunk/net-analyzer/autoscan-network) (version `1.50`).

## Gentoo / Pentoo

```bash
sudo eselect repository add pentoo git https://github.com/pentoo/pentoo.git
sudo emerge --ask autoscan-network
```

## Arch / BlackArch

Not packaged — build from source below (AUR unlikely; check `paru -Ss autoscan-network`).

## Any other Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](http://autoscan-network.com/) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from ebuild RDEPEND/DEPEND)

- Gentoo packages: `dev-libs/glib-2.6.3`, `dev-libs/libelf`, `gnome-base/gnome-keyring-0.4.2`, `gnome-base/gnome-vfs-2.8.4`, `gnome-base/libgnomeui-2.0`, `gnome-extra/gtkhtml-2.0`, `media-libs/libao-0.8.5`, `media-libs/libvorbis-1.1.0`, `net-analyzer/nessus`, `net-analyzer/net-snmp-5.0`, `net-analyzer/nmap`, `net-fs/samba`, `net-libs/gtk-vnc-0.2.0`, `virtual/pkgconfig`

Backlinks: [[scanner]] · [[autoscan-network]] · [[Pentoo Gap Analysis]]
