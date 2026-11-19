---
tool: "gr-dect2"
build-system: "cmake"
upstream: "https://github.com/pavelyazev/gr-dect2"
recipe: "blackarch"
nix-package: "gr-dect2"
source: "https://github.com/pavelyazev/gr-dect2"
license: "custom:unknown"
blackarch-pkgver: "39.0d973fe"
---

# Installing gr-dect2

> Real-time DECT voice channel decoding by Gnuradio.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/gr-dect2) (version `39.0d973fe`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S gr-dect2    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + cmake):**

**Source:** download the release from the [upstream site](https://github.com/pavelyazev/gr-dect2) (the recipe uses a versioned archive).

```bash
cmake -B build -DCMAKE_BUILD_TYPE=Release
cmake --build build -j
sudo cmake --install build
```

## Dependencies (from recipe)

- **Build tools**: `cmake`, `git`
- **System libraries** (Arch name — translate for your distro):
  - `boost`
  - `boost-libs`
  - `doxygen`
  - `gnuradio`
  - `gnuradio-osmosdr`
  - `hackrf`
  - `pybind11`
  - `swig`

## Exact build steps from the recipe

```bash
cd $pkgname

  mkdir -p build
  cd build
  cmake \
    -DCMAKE_INSTALL_PREFIX=/usr \
    -DCMAKE_BUILD_TYPE=release \
  ..
  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[radio]] · [[gr-dect2]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#gr-dect2    # ad-hoc run, no install
nix profile install nixpkgs#gr-dect2   # or persist it
```
