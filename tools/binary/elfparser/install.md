---
tool: "elfparser"
build-system: "cmake"
upstream: "https://github.com/jacob-baines/elfparser"
recipe: "blackarch"
source: "https://github.com/jacob-baines/elfparser"
license: "MIT"
blackarch-pkgver: "7.39d21ca"
---

# Installing elfparser

> Cross Platform ELF analysis.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/elfparser) (version `7.39d21ca`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S elfparser    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + cmake):**

**Source:** download the release from the [upstream site](https://github.com/jacob-baines/elfparser) (the recipe uses a versioned archive).

```bash
cmake -B build -DCMAKE_BUILD_TYPE=Release
cmake --build build -j
sudo cmake --install build
```

## Dependencies (from recipe)

- **Build tools**: `cmake`, `git`
- **System libraries** (Arch name — translate for your distro):
  - `boost`

## Exact build steps from the recipe

```bash
cd $pkgname

  mkdir -p build

  cd build

  cmake -DCMAKE_INSTALL_PREFIX:PATH=/usr ..

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[binary]] · [[elfparser]] · [[BlackArch Tools]]

