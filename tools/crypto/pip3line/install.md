---
tool: "pip3line"
build-system: "cmake"
upstream: "https://github.com/nccgroup/pip3line"
recipe: "blackarch"
source: "https://github.com/nccgroup/pip3line"
license: "AGPL3"
blackarch-pkgver: "92.5e27195"
---

# Installing pip3line

> The Swiss army knife of byte manipulation.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/pip3line) (version `92.5e27195`, license AGPL3).

## Arch / BlackArch

```bash
sudo pacman -S pip3line    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + cmake):**

**Source:** download the release from the [upstream site](https://github.com/nccgroup/pip3line) (the recipe uses a versioned archive).

```bash
cmake -B build -DCMAKE_BUILD_TYPE=Release
cmake --build build -j
sudo cmake --install build
```

## Dependencies (from recipe)

- **Build tools**: `cmake`, `git`
- **System libraries** (Arch name — translate for your distro):
  - `distorm`
  - `gcc-libs`
  - `openssl` — Debian libssl-dev / Fedora openssl-devel
  - `python`
  - `python2`
  - `qt4`
  - `xdg-utils`

## Exact build steps from the recipe

```bash
cd $pkgname

  mkdir -p build

  cd build

  cmake -DCMAKE_INSTALL_PREFIX:PATH=/usr -DCMAKE_SKIP_RPATH=ON -DALL=ON \
    -DWITH_DISTORM=ON -DWITH_OPENSSL=ON -DWITH_PYTHON27=ON -DWITH_PYTHON3=ON ../
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[crypto]] · [[pip3line]] · [[BlackArch Tools]]

