---
tool: "elettra"
build-system: "cmake"
upstream: "https://www.winstonsmith.info/julia/elettra/"
recipe: "blackarch"
aur-package: "elettra"
source: "https://www.winstonsmith.info/julia/elettra/"
license: "custom"
blackarch-pkgver: "1.0"
---

# Installing elettra

> A plausible deniable cryptography tool that supports a dynamic number of files and requires a password for each file.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/elettra) (version `1.0`, license custom).

## Arch / BlackArch

```bash
sudo pacman -S elettra    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + cmake):**

**Source:** download the release from the [upstream site](https://www.winstonsmith.info/julia/elettra/) (the recipe uses a versioned archive).

```bash
cmake -B build -DCMAKE_BUILD_TYPE=Release
cmake --build build -j
sudo cmake --install build
```

## Dependencies (from recipe)

- **Build tools**: `cmake`
- **System libraries** (Arch name — translate for your distro):
  - `glibc`
  - `libmcrypt`
  - `mhash`
  - `zlib` — Debian zlib1g-dev / Fedora zlib-devel

## Exact build steps from the recipe

```bash
cd "$pkgname/src"

  cmake -B build
  cmake --build build
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[misc]] · [[elettra]] · [[BlackArch Tools]]

