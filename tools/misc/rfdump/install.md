---
tool: "rfdump"
build-system: "autotools"
upstream: "http://www.rfdump.org"
recipe: "arch-official/extra"
nix-package: "rfdump"
recipe-url: "https://gitlab.archlinux.org/archlinux/packaging/packages/rfdump/-/raw/main/PKGBUILD"
version: "1.6"
license: "GPL2"
---

# Installing rfdump

> tool to decode RFID tag data

Recipe source of truth: Arch official [`PKGBUILD`](https://gitlab.archlinux.org/archlinux/packaging/packages/rfdump/-/raw/main/PKGBUILD) (version `1.6`, license GPL2).

## Arch / BlackArch — prebuilt binary

```bash
sudo pacman -S rfdump
```

## Kali / Debian-based — same package name

```bash
sudo apt install rfdump
```

## Nix (works on any distro)

```bash
nix run nixpkgs#rfdump    # ad-hoc run, no install
nix profile install nixpkgs#rfdump   # or persist it
```

## Any other Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](http://www.rfdump.org/) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `expat`
  - `gtk3` — Debian libgtk-3-dev / Fedora gtk3-devel

## Exact build steps from the recipe

```bash
cd ${pkgname}-${pkgver}
  CFLAGS+=' -fcommon'
  LIBS=-lexpat ./configure --prefix=/usr
  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout

Backlinks: [[misc]] · [[rfdump]] · [[Kali Gap Analysis]]
