---
tool: "wisecracker"
build-system: "cmake"
upstream: "http://selectiveintellect.com/wisecracker.html"
recipe: "pentoo-ebuild"
version: "1.0-r1"
license: "GPL-3"
---

# Installing wisecracker

> An open source framework for tools that can distribute brute force cryptanalysis

Recipe source of truth: Pentoo [`wisecracker.ebuild`](https://github.com/pentoo/pentoo/tree/master/portage/trunk/app-crypt/wisecracker) (version `1.0-r1`).

## Gentoo / Pentoo

```bash
sudo eselect repository add pentoo git https://github.com/pentoo/pentoo.git
sudo emerge --ask wisecracker
```

## Arch / BlackArch

Not packaged — build from source below (AUR unlikely; check `paru -Ss wisecracker`).

## Any other Linux distro

**From source (needs compiler + cmake):**

```bash
git clone https://github.com/vikasnkumar/wisecracker/archive/v1.0.tar.gz
cd v1.0.tar.gz
cmake -B build -DCMAKE_BUILD_TYPE=Release
cmake --build build -j
sudo cmake --install build
```

## Dependencies (from ebuild RDEPEND/DEPEND)

- Gentoo packages: `app-editors/vim`, `dev-libs/openssl`, `virtual/mpi`, `virtual/opencl`

Backlinks: [[cracker]] · [[wisecracker]] · [[Pentoo Gap Analysis]]
