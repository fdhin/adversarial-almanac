---
tool: "rizin"
build-system: "other"
upstream: "https://rizin.re"
recipe: "arch-official/extra"
nix-package: "rizin"
recipe-url: "https://gitlab.archlinux.org/archlinux/packaging/packages/rizin/-/raw/main/PKGBUILD"
version: "0.8.2"
license: "unknown"
---

# Installing rizin

> reverse engineering framework and command-line toolset

Recipe source of truth: Arch official [`PKGBUILD`](https://gitlab.archlinux.org/archlinux/packaging/packages/rizin/-/raw/main/PKGBUILD) (version `0.8.2`, license n/a).

## Arch / BlackArch — prebuilt binary

```bash
sudo pacman -S rizin
```

## Kali / Debian-based — same package name

```bash
sudo apt install rizin
```

## Nix (works on any distro)

```bash
nix run nixpkgs#rizin    # ad-hoc run, no install
nix profile install nixpkgs#rizin   # or persist it
```

## Any other Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://rizin.re/) (the recipe uses a versioned archive).

```bash
# see exact recipe steps below
```

## Exact build steps from the recipe

```bash
cd ${pkgname}-v${pkgver}
  arch-meson build \
    -D use_sys_capstone=enabled \
    -D use_capstone_version=v5 \
    -D use_sys_magic=enabled \
    -D use_sys_libzip=enabled \
    -D use_sys_libzstd=enabled \
    -D use_sys_lz4=enabled \
    -D use_sys_lzma=enabled \
    -D use_sys_zlib=enabled \
    -D use_sys_xxhash=enabled \
    -D use_sys_openssl=enabled \
    -D use_sys_pcre2=enabled \
    -D use_sys_tree_sitter=enabled \
    -D use_sys_libmspack=enabled \
    -D use_zlib=true \
    -D use_lzma=true \
    -D local=disabled
  ninja -C build
```

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout

Backlinks: [[reversing]] · [[rizin]] · [[Kali Gap Analysis]]
