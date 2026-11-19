---
tool: "dirbuster-ng"
build-system: "cmake"
upstream: "https://github.com/digination/dirbuster-ng"
recipe: "blackarch"
source: "https://github.com/digination/dirbuster-ng"
license: "custom:unknown"
blackarch-pkgver: "9.0c34920"
---

# Installing dirbuster-ng

> C CLI implementation of the Java dirbuster tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/dirbuster-ng) (version `9.0c34920`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S dirbuster-ng    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + cmake):**

**Source:** download the release from the [upstream site](https://github.com/digination/dirbuster-ng) (the recipe uses a versioned archive).

```bash
cmake -B build -DCMAKE_BUILD_TYPE=Release
cmake --build build -j
sudo cmake --install build
```

## Dependencies (from recipe)

- **Build tools**: `cmake`, `git`
- **System libraries** (Arch name — translate for your distro):
  - `curl`

## Exact build steps from the recipe

```bash
cd $pkgname

  mkdir -p build

  cd build

  cmake ..

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[dirbuster-ng]] · [[BlackArch Tools]]

