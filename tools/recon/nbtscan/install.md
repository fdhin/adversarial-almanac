---
tool: "nbtscan"
build-system: "autotools"
upstream: "https://github.com/resurrecting-open-source-projects/nbtscan"
recipe: "arch-official/extra"
nix-package: "nbtscan"
recipe-url: "https://gitlab.archlinux.org/archlinux/packaging/packages/nbtscan/-/raw/main/PKGBUILD"
version: "1.7.2"
license: "GPL2"
---

# Installing nbtscan

> scan networks searching for NetBIOS information

Recipe source of truth: Arch official [`PKGBUILD`](https://gitlab.archlinux.org/archlinux/packaging/packages/nbtscan/-/raw/main/PKGBUILD) (version `1.7.2`, license GPL2).

## Arch / BlackArch — prebuilt binary

```bash
sudo pacman -S nbtscan
```

## Kali / Debian-based — same package name

```bash
sudo apt install nbtscan
```

## Nix (works on any distro)

```bash
nix run nixpkgs#nbtscan    # ad-hoc run, no install
nix profile install nixpkgs#nbtscan   # or persist it
```

## Any other Linux distro

**From source (needs compiler + make):**

```bash
git clone https://github.com/resurrecting-open-source-projects/nbtscan.git
cd nbtscan
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `glibc`

## Exact build steps from the recipe

```bash
cd "$pkgname"
  ./configure --prefix=/usr
  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout

Backlinks: [[recon]] · [[nbtscan]] · [[Kali Gap Analysis]]
