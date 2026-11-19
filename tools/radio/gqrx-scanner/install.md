---
tool: "gqrx-scanner"
build-system: "cmake"
upstream: "https://github.com/neural75/gqrx-scanner"
recipe: "blackarch"
aur-package: "gqrx-scanner"
source: "https://github.com/neural75/gqrx-scanner"
license: "MIT"
blackarch-pkgver: "1.0.5"
---

# Installing gqrx-scanner

> A frequency scanner for Gqrx Software Defined Radio receiver.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/gqrx-scanner) (version `1.0.5`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S gqrx-scanner    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + cmake):**

**Source:** download the release from the [upstream site](https://github.com/neural75/gqrx-scanner) (the recipe uses a versioned archive).

```bash
cmake -B build -DCMAKE_BUILD_TYPE=Release
cmake --build build -j
sudo cmake --install build
```

## Dependencies (from recipe)

- **Build tools**: `cmake`
- **System libraries** (Arch name — translate for your distro):
  - `glibc`

## Exact build steps from the recipe

```bash
cmake -B build -S "${pkgname}-${pkgver}" \
    -DCMAKE_BUILD_TYPE='None' \
    -DCMAKE_INSTALL_PREFIX='/usr' \
    -Wno-dev
  cmake --build build
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[radio]] · [[gqrx-scanner]] · [[BlackArch Tools]]

