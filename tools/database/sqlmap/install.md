---
tool: "sqlmap"
build-system: "other"
upstream: "https://sqlmap.org"
recipe: "arch-official/extra"
nix-package: "sqlmap"
recipe-url: "https://gitlab.archlinux.org/archlinux/packaging/packages/sqlmap/-/raw/main/PKGBUILD"
version: "1.10.10"
license: "unknown"
---

# Installing sqlmap

> automatic SQL injection tool

Recipe source of truth: Arch official [`PKGBUILD`](https://gitlab.archlinux.org/archlinux/packaging/packages/sqlmap/-/raw/main/PKGBUILD) (version `1.10.10`, license n/a).

## Arch / BlackArch — prebuilt binary

```bash
sudo pacman -S sqlmap
```

## Kali / Debian-based — same package name

```bash
sudo apt install sqlmap
```

## Nix (works on any distro)

```bash
nix run nixpkgs#sqlmap    # ad-hoc run, no install
nix profile install nixpkgs#sqlmap   # or persist it
```

## Any other Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://sqlmap.org/) (the recipe uses a versioned archive).

```bash
# see exact recipe steps below
```

## Exact build steps from the recipe

```bash
cd ${pkgname}-${pkgver}
  python -m compileall .
  python -O -m compileall .
```

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout

Backlinks: [[database]] · [[sqlmap]] · [[Kali Gap Analysis]]
