---
tool: "gdb"
build-system: "autotools"
upstream: "https://www.gnu.org/software/gdb/"
recipe: "arch-official/extra"
nix-package: "gdb"
recipe-url: "https://gitlab.archlinux.org/archlinux/packaging/packages/gdb/-/raw/main/PKGBUILD"
version: "18.1"
license: "unknown"
---

# Installing gdb

> GNU Debugger

Recipe source of truth: Arch official [`PKGBUILD`](https://gitlab.archlinux.org/archlinux/packaging/packages/gdb/-/raw/main/PKGBUILD) (version `18.1`, license n/a).

## Arch / BlackArch — prebuilt binary

```bash
sudo pacman -S gdb
```

## Kali / Debian-based — same package name

```bash
sudo apt install gdb
```

## Nix (works on any distro)

```bash
nix run nixpkgs#gdb    # ad-hoc run, no install
nix profile install nixpkgs#gdb   # or persist it
```

## Any other Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](https://www.gnu.org/s/gdb/) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Exact build steps from the recipe

```bash
cd gdb-$pkgver

  mkdir -p build && cd build
  ../configure \
    --prefix=/usr \
    --disable-nls \
    --enable-source-highlight \
    --enable-tui \
    --enable-targets=all \
    --enable-languages=all \
    --enable-multilib \
    --enable-interwork \
    --with-system-readline \
    --with-python=/usr/bin/python \
    --with-system-gdbinit=/etc/gdb/gdbinit
  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout
- Data: `/usr/share/gdb`

Backlinks: [[forensic]] · [[gdb]] · [[Kali Gap Analysis]]
