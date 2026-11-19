---
tool: "nfcd"
build-system: "cmake"
upstream: "https://code.google.com/p/nfc-tools/wiki/nfcd"
recipe: "pentoo-ebuild"
version: "9999"
license: "GPL-3"
---

# Installing nfcd

> a daemon which access NFC Devices and Targets

Recipe source of truth: Pentoo [`nfcd.ebuild`](https://github.com/pentoo/pentoo/tree/master/portage/trunk/net-wireless/nfcd) (version `9999`).

## Gentoo / Pentoo

```bash
sudo eselect repository add pentoo git https://github.com/pentoo/pentoo.git
sudo emerge --ask nfcd
```

## Arch / BlackArch

Not packaged — build from source below (AUR unlikely; check `paru -Ss nfcd`).

## Any other Linux distro

**From source (needs compiler + cmake):**

**Source:** download the release from the [upstream site](https://code.google.com/p/nfc-tools/wiki/nfcd) (the recipe uses a versioned archive).

```bash
cmake -B build -DCMAKE_BUILD_TYPE=Release
cmake --build build -j
sudo cmake --install build
```

## Dependencies (from ebuild RDEPEND/DEPEND)

- Gentoo packages: `dev-libs/libndev`, `dev-libs/libnfc`, `net-wireless/libfreefare`

Backlinks: [[wireless]] · [[nfcd]] · [[Pentoo Gap Analysis]]
