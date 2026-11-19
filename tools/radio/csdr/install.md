---
tool: "csdr"
build-system: "cmake"
upstream: "https://github.com/jketterl/csdr"
recipe: "blackarch"
nix-package: "csdr"
aur-package: "csdr"
source: "https://github.com/jketterl/csdr"
license: "GPL-3.0"
blackarch-pkgver: "0.18.2.r68.g1f15b8c"
---

# Installing csdr

> A simple DSP library and command-line tool for Software Defined Radio.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/csdr) (version `0.18.2.r68.g1f15b8c`, license GPL-3.0).

## Arch / BlackArch

```bash
sudo pacman -S csdr    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + cmake):**

**Source:** download the release from the [upstream site](https://github.com/jketterl/csdr) (the recipe uses a versioned archive).

```bash
cmake -B build -DCMAKE_BUILD_TYPE=Release
cmake --build build -j
sudo cmake --install build
```

## Dependencies (from recipe)

- **Build tools**: `cmake`, `git`
- **System libraries** (Arch name — translate for your distro):
  - `fftw`
  - `libsamplerate`

## Exact build steps from the recipe

```bash
cd $pkgname

  mkdir build
  cd build
  cmake -DCMAKE_INSTALL_PREFIX=/usr ..

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[radio]] · [[csdr]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#csdr    # ad-hoc run, no install
nix profile install nixpkgs#csdr   # or persist it
```
