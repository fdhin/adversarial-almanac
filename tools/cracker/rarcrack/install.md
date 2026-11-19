---
tool: "rarcrack"
build-system: "other"
upstream: "https://github.com/ziman/rarcrack"
recipe: "arch-official/extra"
nix-package: "rarcrack"
recipe-url: "https://gitlab.archlinux.org/archlinux/packaging/packages/rarcrack/-/raw/main/PKGBUILD"
version: "0.2"
license: "GPL-2.0-only"
---

# Installing rarcrack

> Password cracker for rar archives

Recipe source of truth: Arch official [`PKGBUILD`](https://gitlab.archlinux.org/archlinux/packaging/packages/rarcrack/-/raw/main/PKGBUILD) (version `0.2`, license GPL-2.0-only).

## Arch / BlackArch — prebuilt binary

```bash
sudo pacman -S rarcrack
```

## Kali / Debian-based — same package name

```bash
sudo apt install rarcrack
```

## Nix (works on any distro)

```bash
nix run nixpkgs#rarcrack    # ad-hoc run, no install
nix profile install nixpkgs#rarcrack   # or persist it
```

## Any other Linux distro

**From source:**

```bash
git clone https://github.com/ziman/rarcrack.git
cd rarcrack
# see exact recipe steps below
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `libxml2.so`
  - `p7zip`
  - `unrar`
  - `unzip`

## Exact build steps from the recipe

```bash
cd ${pkgname}-${pkgver}
  cc ${CFLAGS} ${LDFLAGS} -o rarcrack -pthread rarcrack.c \
    `pkg-config --libs --cflags libxml-2.0`
```

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout

Backlinks: [[cracker]] · [[rarcrack]] · [[Kali Gap Analysis]]
