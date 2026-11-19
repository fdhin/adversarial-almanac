---
tool: "ucsniff"
build-system: "autotools"
upstream: "http://ucsniff.sourceforge.net"
recipe: "pentoo-ebuild"
version: "3.20"
license: "GPL-3"
---

# Installing ucsniff

> VoIP audio & video sniffer

Recipe source of truth: Pentoo [`ucsniff.ebuild`](https://github.com/pentoo/pentoo/tree/master/portage/trunk/net-analyzer/ucsniff) (version `3.20`).

## Gentoo / Pentoo

```bash
sudo eselect repository add pentoo git https://github.com/pentoo/pentoo.git
sudo emerge --ask ucsniff
```

## Arch / BlackArch

Not packaged — build from source below (AUR unlikely; check `paru -Ss ucsniff`).

## Any other Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](http://ucsniff.sourceforge.net) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from ebuild RDEPEND/DEPEND)

- Gentoo packages: `media-libs/alsa-lib`, `media-libs/freetype`, `media-video/libav`, `media-video/vlc`, `net-libs/libnet`, `net-libs/libpcap`, `sys-libs/zlib`, `x11-libs/libXext`

Backlinks: [[scanner]] · [[ucsniff]] · [[Pentoo Gap Analysis]]
