---
tool: "flashrom"
build-system: "other"
upstream: "https://www.flashrom.org/"
recipe: "arch-official/extra"
nix-package: "flashrom"
recipe-url: "https://gitlab.archlinux.org/archlinux/packaging/packages/flashrom/-/raw/main/PKGBUILD"
version: "1.8.0"
license: "unknown"
---

# Installing flashrom

> Identify, read, write, erase, and verify BIOS/ROM/flash chips

Recipe source of truth: Arch official [`PKGBUILD`](https://gitlab.archlinux.org/archlinux/packaging/packages/flashrom/-/raw/main/PKGBUILD) (version `1.8.0`, license n/a).

## Arch / BlackArch — prebuilt binary

```bash
sudo pacman -S flashrom
```

## Kali / Debian-based — same package name

```bash
sudo apt install flashrom
```

## Nix (works on any distro)

```bash
nix run nixpkgs#flashrom    # ad-hoc run, no install
nix profile install nixpkgs#flashrom   # or persist it
```

## Any other Linux distro

**From source:**

```bash
git clone https://github.com/flashrom/flashrom.git
cd flashrom
# see exact recipe steps below
```

## Exact build steps from the recipe

```bash
arch-meson ${pkgname} build
  meson compile -C build
```

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout

Backlinks: [[hardware]] · [[flashrom]] · [[Kali Gap Analysis]]
