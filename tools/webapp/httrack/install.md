---
tool: "httrack"
build-system: "autotools"
upstream: "https://www.httrack.com/"
recipe: "arch-official/extra"
nix-package: "httrack"
recipe-url: "https://gitlab.archlinux.org/archlinux/packaging/packages/httrack/-/raw/main/PKGBUILD"
version: "3.50.5"
license: "GPL-3.0-or-later"
---

# Installing httrack

> Copy websites to your computer (Offline browser)

Recipe source of truth: Arch official [`PKGBUILD`](https://gitlab.archlinux.org/archlinux/packaging/packages/httrack/-/raw/main/PKGBUILD) (version `3.50.5`, license GPL-3.0-or-later).

## Arch / BlackArch — prebuilt binary

```bash
sudo pacman -S httrack
```

## Kali / Debian-based — same package name

```bash
sudo apt install httrack
```

## Nix (works on any distro)

```bash
nix run nixpkgs#httrack    # ad-hoc run, no install
nix profile install nixpkgs#httrack   # or persist it
```

## Any other Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](https://www.httrack.com) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `bash`
  - `hicolor-icon-theme` — hicolor-icon-theme (any distro)
  - `openssl` — Debian libssl-dev / Fedora openssl-devel
  - `xdg-utils`

## Exact build steps from the recipe

```bash
cd "${srcdir}/${pkgname}-${pkgver}"

  ./configure --prefix=/usr --enable-static=no
  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout

Backlinks: [[webapp]] · [[httrack]] · [[Kali Gap Analysis]]
