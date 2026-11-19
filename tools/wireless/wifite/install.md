---
tool: "wifite"
build-system: "python"
upstream: "https://github.com/kimocoder/wifite2"
recipe: "arch-official/extra"
recipe-url: "https://gitlab.archlinux.org/archlinux/packaging/packages/wifite/-/raw/main/PKGBUILD"
version: "2.8.2"
license: "GPL-2.0-only"
---

# Installing wifite

> Python script to automate wireless auditing using aircrack-ng tools

Recipe source of truth: Arch official [`PKGBUILD`](https://gitlab.archlinux.org/archlinux/packaging/packages/wifite/-/raw/main/PKGBUILD) (version `2.8.2`, license GPL-2.0-only).

## Arch / BlackArch — prebuilt binary

```bash
sudo pacman -S wifite
```

## Kali / Debian-based — same package name

```bash
sudo apt install wifite
```

## Any other Linux distro

**B. From source:**

```bash
git clone https://github.com/kimocoder/wifite2.git
cd wifite2
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Exact build steps from the recipe

```bash
cd "${pkgname}2-${pkgver}"

  python -m build --wheel --no-isolation
```

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout

Backlinks: [[wireless]] · [[wifite]] · [[Kali Gap Analysis]]
