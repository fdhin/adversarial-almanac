---
tool: "inspectrum"
build-system: "cmake"
upstream: "https://github.com/miek/inspectrum"
recipe: "arch-official/extra"
nix-package: "inspectrum"
recipe-url: "https://gitlab.archlinux.org/archlinux/packaging/packages/inspectrum/-/raw/main/PKGBUILD"
version: "0.4.0"
license: "GPL-3.0-or-later"
---

# Installing inspectrum

> tool for visualising captured radio signals

Recipe source of truth: Arch official [`PKGBUILD`](https://gitlab.archlinux.org/archlinux/packaging/packages/inspectrum/-/raw/main/PKGBUILD) (version `0.4.0`, license GPL-3.0-or-later).

## Arch / BlackArch — prebuilt binary

```bash
sudo pacman -S inspectrum
```

## Kali / Debian-based — same package name

```bash
sudo apt install inspectrum
```

## Nix (works on any distro)

```bash
nix run nixpkgs#inspectrum    # ad-hoc run, no install
nix profile install nixpkgs#inspectrum   # or persist it
```

## Any other Linux distro

**From source (needs compiler + cmake):**

```bash
git clone https://github.com/miek/inspectrum.git
cd inspectrum
cmake -B build -DCMAKE_BUILD_TYPE=Release
cmake --build build -j
sudo cmake --install build
```

## Dependencies (from recipe)

- **Build tools**: `cmake`
- **System libraries** (Arch name — translate for your distro):
  - `fftw`
  - `liquid-dsp`
  - `qt5-base`

## Exact build steps from the recipe

```bash
mkdir -p $pkgname-$pkgver/build
  cd $pkgname-$pkgver/build

  cmake .. \
    -DCMAKE_INSTALL_PREFIX=/usr \
    -DCMAKE_POLICY_VERSION_MINIMUM=3.5

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout

Backlinks: [[radio]] · [[inspectrum]] · [[Kali Gap Analysis]]
