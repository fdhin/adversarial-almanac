---
tool: "tcpdump"
build-system: "cmake"
upstream: "https://www.tcpdump.org/"
recipe: "arch-official/extra"
nix-package: "tcpdump"
recipe-url: "https://gitlab.archlinux.org/archlinux/packaging/packages/tcpdump/-/raw/main/PKGBUILD"
version: "4.99.7"
license: "unknown"
---

# Installing tcpdump

> command-line network traffic analyzer

Recipe source of truth: Arch official [`PKGBUILD`](https://gitlab.archlinux.org/archlinux/packaging/packages/tcpdump/-/raw/main/PKGBUILD) (version `4.99.7`, license n/a).

## Arch / BlackArch — prebuilt binary

```bash
sudo pacman -S tcpdump
```

## Kali / Debian-based — same package name

```bash
sudo apt install tcpdump
```

## Nix (works on any distro)

```bash
nix run nixpkgs#tcpdump    # ad-hoc run, no install
nix profile install nixpkgs#tcpdump   # or persist it
```

## Any other Linux distro

**From source (needs compiler + cmake):**

**Source:** download the release from the [upstream site](https://www.tcpdump.org/) (the recipe uses a versioned archive).

```bash
cmake -B build -DCMAKE_BUILD_TYPE=Release
cmake --build build -j
sudo cmake --install build
```

## Exact build steps from the recipe

```bash
local cmake_options=(
    -DCMAKE_INSTALL_PREFIX=/usr
    -DCMAKE_BUILD_TYPE=None
    -Wno-dev
    -B build
    -S $pkgname-$pkgver
  )

  cmake "${cmake_options[@]}"
  cmake --build build --verbose
```

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout

Backlinks: [[forensic]] · [[tcpdump]] · [[Kali Gap Analysis]]
