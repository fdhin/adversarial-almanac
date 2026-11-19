---
tool: "radare2"
build-system: "other"
upstream: "https://radare.org"
recipe: "arch-official/extra"
nix-package: "radare2"
recipe-url: "https://gitlab.archlinux.org/archlinux/packaging/packages/radare2/-/raw/main/PKGBUILD"
version: "6.2.2"
license: "GPL-3.0-only AND LGPL-3.0-only"
---

# Installing radare2

> free and advanced command line hexadecimal editor

Recipe source of truth: Arch official [`PKGBUILD`](https://gitlab.archlinux.org/archlinux/packaging/packages/radare2/-/raw/main/PKGBUILD) (version `6.2.2`, license GPL-3.0-only AND LGPL-3.0-only).

## Arch / BlackArch — prebuilt binary

```bash
sudo pacman -S radare2
```

## Kali / Debian-based — same package name

```bash
sudo apt install radare2
```

## Nix (works on any distro)

```bash
nix run nixpkgs#radare2    # ad-hoc run, no install
nix profile install nixpkgs#radare2   # or persist it
```

## Any other Linux distro

**From source:**

```bash
git clone https://github.com/radareorg/radare2.git
cd radare2
# see exact recipe steps below
```

## Exact build steps from the recipe

```bash
cd $pkgname
  export MESON_PACKAGE_CACHE_DIR="$srcdir"
  arch-meson build \
    -D use_sys_capstone=true \
    -D use_capstone_version=v5 \
    -D use_sys_magic=true \
    -D use_sys_zip=true \
    -D use_sys_zlib=true \
    -D use_sys_lz4=true \
    -D use_sys_xxhash=true \
    -D use_sys_zydis=true \
    -D use_libuv=true \
    -D use_webui=true
  meson compile -C build
```

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout
- Data: `/usr/share/man`

Backlinks: [[forensic]] · [[radare2]] · [[Kali Gap Analysis]]
