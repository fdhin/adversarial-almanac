---
tool: "sylkie"
build-system: "cmake"
upstream: "https://github.com/dlrobertson/sylkie"
recipe: "blackarch"
source: "https://github.com/dlrobertson/sylkie"
license: "MIT"
blackarch-pkgver: "0.0.4.r3.g1cf170f"
---

# Installing sylkie

> IPv6 address spoofing with the Neighbor Discovery Protocol.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/sylkie) (version `0.0.4.r3.g1cf170f`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S sylkie    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + cmake):**

**Source:** download the release from the [upstream site](https://github.com/dlrobertson/sylkie) (the recipe uses a versioned archive).

```bash
cmake -B build -DCMAKE_BUILD_TYPE=Release
cmake --build build -j
sudo cmake --install build
```

## Dependencies (from recipe)

- **Build tools**: `cmake`, `git`
- **System libraries** (Arch name — translate for your distro):
  - `json-c`
  - `libseccomp`

## Exact build steps from the recipe

```bash
cd $pkgname

  mkdir -p build

  cd build

  cmake -DCMAKE_INSTALL_PREFIX=/usr -DENABLE_SETUID=ON ..

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[spoof]] · [[sylkie]] · [[BlackArch Tools]]

