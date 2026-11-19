---
tool: "aptdec"
build-system: "cmake"
upstream: "https://github.com/Xerbo/aptdec"
recipe: "blackarch"
nix-package: "aptdec"
source: "https://github.com/cofyc/argparse.git"
license: "GPL-2.0-or-later"
blackarch-pkgver: "v1.8.0.r2.g7db2030"
---

# Installing aptdec

> NOAA APT satellite imagery decoder.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/aptdec) (version `v1.8.0.r2.g7db2030`, license GPL-2.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S aptdec    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + cmake):**

```bash
git clone https://github.com/cofyc/argparse.git
cd argparse
cmake -B build -DCMAKE_BUILD_TYPE=Release
cmake --build build -j
sudo cmake --install build
```

## Dependencies (from recipe)

- **Build tools**: `cmake`, `git`
- **System libraries** (Arch name — translate for your distro):
  - `libpng`
  - `libsndfile`

## Exact build steps from the recipe

```bash
cd $pkgname

  cmake -B build \
    -DCMAKE_BUILD_TYPE=None \
    -DCMAKE_INSTALL_PREFIX=/usr

  make -C build
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[radio]] · [[aptdec]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#aptdec    # ad-hoc run, no install
nix profile install nixpkgs#aptdec   # or persist it
```
