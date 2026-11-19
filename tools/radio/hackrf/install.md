---
tool: "hackrf"
build-system: "cmake"
upstream: "https://github.com/greatscottgadgets/hackrf"
recipe: "arch-official/extra"
nix-package: "hackrf"
recipe-url: "https://gitlab.archlinux.org/archlinux/packaging/packages/hackrf/-/raw/main/PKGBUILD"
version: "2026.01.3"
license: "GPL-2.0-or-later"
---

# Installing hackrf

> Software defined radio peripheral - utilities

Recipe source of truth: Arch official [`PKGBUILD`](https://gitlab.archlinux.org/archlinux/packaging/packages/hackrf/-/raw/main/PKGBUILD) (version `2026.01.3`, license GPL-2.0-or-later).

## Arch / BlackArch — prebuilt binary

```bash
sudo pacman -S hackrf
```

## Kali / Debian-based — same package name

```bash
sudo apt install hackrf
```

## Nix (works on any distro)

```bash
nix run nixpkgs#hackrf    # ad-hoc run, no install
nix profile install nixpkgs#hackrf   # or persist it
```

## Any other Linux distro

**From source (needs compiler + cmake):**

```bash
git clone https://github.com/greatscottgadgets/hackrf.git
cd hackrf
cmake -B build -DCMAKE_BUILD_TYPE=Release
cmake --build build -j
sudo cmake --install build
```

## Exact build steps from the recipe

```bash
cd $pkgname-$pkgver
  export CMAKE_POLICY_VERSION_MINIMUM=3.5
  cmake -S host -B build \
    -DCMAKE_BUILD_TYPE=None \
    -DCMAKE_INSTALL_PREFIX=/usr \
    -Wno-dev
  cmake --build build
```

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout
- Data: `/usr/share/hackrf`

Backlinks: [[radio]] · [[hackrf]] · [[Kali Gap Analysis]]
