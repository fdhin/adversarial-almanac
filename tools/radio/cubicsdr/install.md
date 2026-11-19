---
tool: "cubicsdr"
build-system: "cmake"
upstream: "https://github.com/cjcliffe/CubicSDR"
recipe: "blackarch"
nix-package: "cubicsdr"
aur-package: "cubicsdr"
source: "https://github.com/cjcliffe/CubicSDR"
license: "GPL2"
blackarch-pkgver: "0.2.7"
---

# Installing cubicsdr

> Cross-Platform Software-Defined Radio Application.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/cubicsdr) (version `0.2.7`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S cubicsdr    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + cmake):**

**Source:** download the release from the [upstream site](https://github.com/cjcliffe/CubicSDR) (the recipe uses a versioned archive).

```bash
cmake -B build -DCMAKE_BUILD_TYPE=Release
cmake --build build -j
sudo cmake --install build
```

## Dependencies (from recipe)

- **Build tools**: `cmake`
- **System libraries** (Arch name — translate for your distro):
  - `freeglut`
  - `gcc-libs`
  - `glibc`
  - `hamlib`
  - `hicolor-icon-theme` — hicolor-icon-theme (any distro)
  - `libglvnd`
  - `libpulse`
  - `liquid-dsp`
  - `soapysdr`
  - `wxwidgets-common`
  - `wxwidgets-gtk3`

## Exact build steps from the recipe

```bash
cd "$_pkgname-$pkgver"

  cmake -S . -B build \
    -DCMAKE_BUILD_TYPE=None \
    -DCMAKE_INSTALL_PREFIX=/usr \
    -Wno-dev \
    -DUSE_HAMLIB=ON \
    -DENABLE_DIGITAL_LAB=ON
  cmake --build build
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/applications`, `/usr/share/cubicsdr`, `/usr/share/icons`

Backlinks: [[radio]] · [[cubicsdr]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#cubicsdr    # ad-hoc run, no install
nix profile install nixpkgs#cubicsdr   # or persist it
```
