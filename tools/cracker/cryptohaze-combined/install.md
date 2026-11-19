---
tool: "cryptohaze-combined"
build-system: "cmake"
upstream: "http://www.cryptohaze.com/"
recipe: "pentoo-ebuild"
version: "9999"
license: "GPL-2"
---

# Installing cryptohaze-combined

> GPU and OpenCL accelerated password auditing tools for security professionals

Recipe source of truth: Pentoo [`cryptohaze-combined.ebuild`](https://github.com/pentoo/pentoo/tree/master/portage/trunk/app-crypt/cryptohaze-combined) (version `9999`).

## Gentoo / Pentoo

```bash
sudo eselect repository add pentoo git https://github.com/pentoo/pentoo.git
sudo emerge --ask cryptohaze-combined
```

## Arch / BlackArch

Not packaged — build from source below (AUR unlikely; check `paru -Ss cryptohaze-combined`).

## Any other Linux distro

**From source (needs compiler + cmake):**

**Source:** download the release from the [upstream site](http://www.cryptohaze.com/) (the recipe uses a versioned archive).

```bash
cmake -B build -DCMAKE_BUILD_TYPE=Release
cmake --build build -j
sudo cmake --install build
```

## Dependencies (from ebuild RDEPEND/DEPEND)

- Gentoo packages: `dev-libs/argtable`, `dev-libs/boost-1.48.0`, `dev-libs/protobuf`, `dev-util/nvidia-cuda-sdk`, `dev-util/nvidia-cuda-toolkit-5.5.22`, `net-misc/curl`, `x11-drivers/ati-drivers`, `x11-drivers/nvidia-drivers`

Backlinks: [[cracker]] · [[cryptohaze-combined]] · [[Pentoo Gap Analysis]]
