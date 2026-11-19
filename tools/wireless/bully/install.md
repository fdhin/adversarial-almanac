---
tool: "bully"
build-system: "make"
upstream: "https://github.com/kimocoder/bully"
recipe: "arch-official/extra"
nix-package: "bully"
recipe-url: "https://gitlab.archlinux.org/archlinux/packaging/packages/bully/-/raw/main/PKGBUILD"
version: "1.4.00"
license: "unknown"
---

# Installing bully

> Implementation of the WPS brute force attack, written in C

Recipe source of truth: Arch official [`PKGBUILD`](https://gitlab.archlinux.org/archlinux/packaging/packages/bully/-/raw/main/PKGBUILD) (version `1.4.00`, license n/a).

## Arch / BlackArch — prebuilt binary

```bash
sudo pacman -S bully
```

## Kali / Debian-based — same package name

```bash
sudo apt install bully
```

## Nix (works on any distro)

```bash
nix run nixpkgs#bully    # ad-hoc run, no install
nix profile install nixpkgs#bully   # or persist it
```

## Any other Linux distro

**From source (needs gcc + make):**

```bash
git clone https://github.com/kimocoder/bully.git
cd bully
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Exact build steps from the recipe

```bash
cd ${pkgname}-${_pkgver}
  make -C src
```

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout

Backlinks: [[wireless]] · [[bully]] · [[Kali Gap Analysis]]
