---
tool: "parted"
build-system: "autotools"
upstream: "https://www.gnu.org/software/parted/parted.html"
recipe: "arch-official/extra"
nix-package: "parted"
recipe-url: "https://gitlab.archlinux.org/archlinux/packaging/packages/parted/-/raw/main/PKGBUILD"
version: "3.8"
license: "GPL-3.0-or-later"
---

# Installing parted

> disk partition manipulator

Recipe source of truth: Arch official [`PKGBUILD`](https://gitlab.archlinux.org/archlinux/packaging/packages/parted/-/raw/main/PKGBUILD) (version `3.8`, license GPL-3.0-or-later).

## Arch / BlackArch — prebuilt binary

```bash
sudo pacman -S parted
```

## Kali / Debian-based — same package name

```bash
sudo apt install parted
```

## Nix (works on any distro)

```bash
nix run nixpkgs#parted    # ad-hoc run, no install
nix profile install nixpkgs#parted   # or persist it
```

## Any other Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](https://www.gnu.org/software/parted) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `device-mapper`
  - `glibc`
  - `libblkid.so`
  - `libdevmapper.so`
  - `libncursesw.so`
  - `libuuid.so`
  - `ncurses`
  - `pkg-config`
  - `readline`
  - `util-linux-libs`

## Exact build steps from the recipe

```bash
cd "${srcdir}/${pkgname}-${pkgver}"

  # Do *not* add --disable-debug, it prevents building fatresize
  # https://github.com/ya-mouse/fatresize/issues/9
  ./configure \
    --prefix=/usr \
    --sbindir=/usr/bin \
    --disable-rpath
  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout

Backlinks: [[forensic]] · [[parted]] · [[Kali Gap Analysis]]
