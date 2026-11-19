---
tool: "faifa"
build-system: "autotools"
upstream: "https://github.com/ffainelli/faifa"
recipe: "pentoo-ebuild"
version: "9999"
license: "BSD"
---

# Installing faifa

> Power Line Communication device manager

Recipe source of truth: Pentoo [`faifa.ebuild`](https://github.com/pentoo/pentoo/tree/master/portage/trunk/net-misc/faifa) (version `9999`).

## Gentoo / Pentoo

```bash
sudo eselect repository add pentoo git https://github.com/pentoo/pentoo.git
sudo emerge --ask faifa
```

## Arch / BlackArch

Not packaged — build from source below (AUR unlikely; check `paru -Ss faifa`).

## Any other Linux distro

**From source (needs compiler + make):**

```bash
git clone https://github.com/ffainelli/faifa
cd faifa
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from ebuild RDEPEND/DEPEND)

- Gentoo packages: `dev-libs/libevent`

Backlinks: [[misc]] · [[faifa]] · [[Pentoo Gap Analysis]]
