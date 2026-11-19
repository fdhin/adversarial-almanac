---
tool: "lynis"
build-system: "scripts"
upstream: "https://cisofy.com/lynis/"
recipe: "arch-official/extra"
nix-package: "lynis"
recipe-url: "https://gitlab.archlinux.org/archlinux/packaging/packages/lynis/-/raw/main/PKGBUILD"
version: "3.1.7"
license: "unknown"
---

# Installing lynis

> security auditing tool for Unix based systems

Recipe source of truth: Arch official [`PKGBUILD`](https://gitlab.archlinux.org/archlinux/packaging/packages/lynis/-/raw/main/PKGBUILD) (version `3.1.7`, license n/a).

## Arch / BlackArch — prebuilt binary

```bash
sudo pacman -S lynis
```

## Kali / Debian-based — same package name

```bash
sudo apt install lynis
```

## Nix (works on any distro)

```bash
nix run nixpkgs#lynis    # ad-hoc run, no install
nix profile install nixpkgs#lynis   # or persist it
```

## Any other Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://cisofy.com/lynis/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout

Backlinks: [[forensic]] · [[lynis]] · [[Kali Gap Analysis]]
