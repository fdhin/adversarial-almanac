---
tool: "edb"
build-system: "cmake"
upstream: "https://github.com/eteran/edb-debugger/"
recipe: "blackarch"
nix-package: "edb"
source: "https://github.com/eteran/edb-debugger/"
license: "GPL2"
blackarch-pkgver: "3355.d3dab734"
---

# Installing edb

> A cross platform AArch32/x86/x86 debugger.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/edb) (version `3355.d3dab734`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S edb    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + cmake):**

```bash
git clone https://github.com/eteran/edb-debugger.git
cd edb-debugger
cmake -B build -DCMAKE_BUILD_TYPE=Release
cmake --build build -j
sudo cmake --install build
```

## Dependencies (from recipe)

- **Build tools**: `cmake`, `git`
- **System libraries** (Arch name — translate for your distro):
  - `boost`
  - `capstone`
  - `graphviz`
  - `gtk-update-icon-cache`
  - `qt5-svg`
  - `qt5-tools`
  - `qt5-xmlpatterns`

## Exact build steps from the recipe

```bash
cd $pkgname

  mkdir -p build

  cd build

  cmake -DCMAKE_INSTALL_PREFIX=/usr/ -DCMAKE_BUILD_TYPE="Release" ..

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/applications`, `/usr/share/icons`, `/usr/share/pixmaps`

Backlinks: [[debugger]] · [[edb]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#edb    # ad-hoc run, no install
nix profile install nixpkgs#edb   # or persist it
```
