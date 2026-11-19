---
tool: "qbdi"
build-system: "cmake"
upstream: "https://github.com/QBDI/QBDI"
recipe: "blackarch"
source: "https://github.com/QBDI/QBDI"
license: "Apache-2.0"
blackarch-pkgver: "v0.12.1.r12.g3318580"
---

# Installing qbdi

> A Dynamic Binary Instrumentation framework based on LLVM.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/qbdi) (version `v0.12.1.r12.g3318580`, license Apache-2.0).

## Arch / BlackArch

```bash
sudo pacman -S qbdi    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + cmake):**

**Source:** download the release from the [upstream site](https://github.com/QBDI/QBDI) (the recipe uses a versioned archive).

```bash
cmake -B build -DCMAKE_BUILD_TYPE=Release
cmake --build build -j
sudo cmake --install build
```

## Dependencies (from recipe)

- **Build tools**: `cmake`, `gcc`, `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Exact build steps from the recipe

```bash
cd $_pkgname

  install -dm 755 build

  cd build

  cmake .. \
    -DCMAKE_INSTALL_PREFIX=/usr \
    -DCMAKE_BUILD_TYPE=Release \
    -DCMAKE_CROSSCOMPILING=FALSE \
    -DQBDI_PLATFORM=linux \
    -DQBDI_ARCH=X86_64

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[binary]] · [[qbdi]] · [[BlackArch Tools]]

