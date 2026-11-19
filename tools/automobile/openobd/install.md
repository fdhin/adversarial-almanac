---
tool: "openobd"
build-system: "cmake"
upstream: ""
recipe: "pentoo-ebuild"
version: "9999"
license: "unknown"
---

# Installing openobd

> neat

Recipe source of truth: Pentoo [`openobd.ebuild`](https://github.com/pentoo/pentoo/tree/master/portage/trunk/app-misc/openobd) (version `9999`).

## Gentoo / Pentoo

```bash
sudo eselect repository add pentoo git https://github.com/pentoo/pentoo.git
sudo emerge --ask openobd
```

## Arch / BlackArch

Not packaged — build from source below (AUR unlikely; check `paru -Ss openobd`).

## Any other Linux distro

**From source (needs compiler + cmake):**

**Source:** download the release from the the upstream site (the recipe uses a versioned archive).

```bash
cmake -B build -DCMAKE_BUILD_TYPE=Release
cmake --build build -j
sudo cmake --install build
```

Backlinks: [[automobile]] · [[openobd]] · [[Pentoo Gap Analysis]]
