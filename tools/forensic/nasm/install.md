---
tool: "nasm"
build-system: "autotools"
upstream: "https://www.nasm.us"
recipe: "arch-official/extra"
nix-package: "nasm"
recipe-url: "https://gitlab.archlinux.org/archlinux/packaging/packages/nasm/-/raw/main/PKGBUILD"
version: "3.02"
license: "unknown"
---

# Installing nasm

> General-purpose x86 assembler

Recipe source of truth: Arch official [`PKGBUILD`](https://gitlab.archlinux.org/archlinux/packaging/packages/nasm/-/raw/main/PKGBUILD) (version `3.02`, license n/a).

## Arch / BlackArch — prebuilt binary

```bash
sudo pacman -S nasm
```

## Kali / Debian-based — same package name

```bash
sudo apt install nasm
```

## Nix (works on any distro)

```bash
nix run nixpkgs#nasm    # ad-hoc run, no install
nix profile install nixpkgs#nasm   # or persist it
```

## Any other Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](https://www.nasm.us/) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Exact build steps from the recipe

```bash
cd ${pkgname}-${pkgver}
  ./configure \
    --prefix=/usr \
    --docdir=/usr/share/doc/${pkgname} \
    --htmldir=/usr/share/doc/${pkgname}/html

  make all doc
```

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout

Backlinks: [[forensic]] · [[nasm]] · [[Kali Gap Analysis]]
