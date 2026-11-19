---
tool: "ext3grep"
build-system: "autotools"
upstream: "https://code.google.com/archive/p/ext3grep/"
recipe: "arch-official/extra"
nix-package: "ext3grep"
recipe-url: "https://gitlab.archlinux.org/archlinux/packaging/packages/ext3grep/-/raw/main/PKGBUILD"
version: "0.10.2"
license: "GPL"
---

# Installing ext3grep

> tool to help recover deleted files on ext3 filesystems

Recipe source of truth: Arch official [`PKGBUILD`](https://gitlab.archlinux.org/archlinux/packaging/packages/ext3grep/-/raw/main/PKGBUILD) (version `0.10.2`, license GPL).

## Arch / BlackArch — prebuilt binary

```bash
sudo pacman -S ext3grep
```

## Kali / Debian-based — same package name

```bash
sudo apt install ext3grep
```

## Nix (works on any distro)

```bash
nix run nixpkgs#ext3grep    # ad-hoc run, no install
nix profile install nixpkgs#ext3grep   # or persist it
```

## Any other Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](https://code.google.com/archive/p/ext3grep/) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **Build tools**: `patch`
- **System libraries** (Arch name — translate for your distro):
  - `e2fsprogs`

## Exact build steps from the recipe

```bash
cd ${pkgname}-${pkgver}

  ./configure --prefix=/usr
  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout

Backlinks: [[forensic]] · [[ext3grep]] · [[Kali Gap Analysis]]
