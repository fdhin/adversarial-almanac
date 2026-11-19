---
tool: "vomit"
build-system: "autotools"
upstream: "http://vomit.xtdnet.nl/"
recipe: "pentoo-ebuild"
aur-package: "vomit"
version: "0.2c"
license: "BSD"
---

# Installing vomit

> Converts a Cisco IP phone conversation into a wav file

Recipe source of truth: Pentoo [`vomit.ebuild`](https://github.com/pentoo/pentoo/tree/master/portage/trunk/net-analyzer/vomit) (version `0.2c`).

## Gentoo / Pentoo

```bash
sudo eselect repository add pentoo git https://github.com/pentoo/pentoo.git
sudo emerge --ask vomit
```

## Arch / BlackArch

Not packaged — build from source below (AUR unlikely; check `paru -Ss vomit`).

## Any other Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](http://vomit.xtdnet.nl/) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from ebuild RDEPEND/DEPEND)

- Gentoo packages: `dev-libs/libdnet`, `dev-libs/libevent`, `net-libs/libpcap`

Backlinks: [[scanner]] · [[vomit]] · [[Pentoo Gap Analysis]]
