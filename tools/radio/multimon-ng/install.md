---
tool: "multimon-ng"
build-system: "cmake"
upstream: "https://github.com/EliasOenal/multimon-ng"
recipe: "arch-official/extra"
nix-package: "multimon-ng"
recipe-url: "https://gitlab.archlinux.org/archlinux/packaging/packages/multimon-ng/-/raw/main/PKGBUILD"
version: "1.6.2"
license: "GPL-2.0-only"
---

# Installing multimon-ng

> digital radio transmission decoder

Recipe source of truth: Arch official [`PKGBUILD`](https://gitlab.archlinux.org/archlinux/packaging/packages/multimon-ng/-/raw/main/PKGBUILD) (version `1.6.2`, license GPL-2.0-only).

## Arch / BlackArch — prebuilt binary

```bash
sudo pacman -S multimon-ng
```

## Kali / Debian-based — same package name

```bash
sudo apt install multimon-ng
```

## Nix (works on any distro)

```bash
nix run nixpkgs#multimon-ng    # ad-hoc run, no install
nix profile install nixpkgs#multimon-ng   # or persist it
```

## Any other Linux distro

**From source (needs compiler + cmake):**

```bash
git clone https://github.com/EliasOenal/multimon-ng.git
cd multimon-ng
cmake -B build -DCMAKE_BUILD_TYPE=Release
cmake --build build -j
sudo cmake --install build
```

## Dependencies (from recipe)

- **Build tools**: `cmake`
- **System libraries** (Arch name — translate for your distro):
  - `glibc`
  - `libpulse`

## Exact build steps from the recipe

```bash
export CMAKE_POLICY_VERSION_MINIMUM=3.5
  cmake -S $pkgname-$pkgver -B build \
    -DCMAKE_BUILD_TYPE=None \
    -DCMAKE_INSTALL_PREFIX=/usr \
    -Wno-dev
  cmake --build build
```

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout

Backlinks: [[radio]] · [[multimon-ng]] · [[Kali Gap Analysis]]
