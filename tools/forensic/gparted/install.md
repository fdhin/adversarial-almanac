---
tool: "gparted"
build-system: "autotools"
upstream: "https://gparted.org/"
recipe: "arch-official/extra"
nix-package: "gparted"
recipe-url: "https://gitlab.archlinux.org/archlinux/packaging/packages/gparted/-/raw/main/PKGBUILD"
version: "1.8.1"
license: "GPL-2.0-or-later, GFDL-1.2-or-later"
---

# Installing gparted

> GNOME partition editor

Recipe source of truth: Arch official [`PKGBUILD`](https://gitlab.archlinux.org/archlinux/packaging/packages/gparted/-/raw/main/PKGBUILD) (version `1.8.1`, license GPL-2.0-or-later, GFDL-1.2-or-later).

## Arch / BlackArch — prebuilt binary

```bash
sudo pacman -S gparted
```

## Kali / Debian-based — same package name

```bash
sudo apt install gparted
```

## Nix (works on any distro)

```bash
nix run nixpkgs#gparted    # ad-hoc run, no install
nix profile install nixpkgs#gparted   # or persist it
```

## Any other Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](https://gparted.org) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `atkmm`
  - `bash`
  - `cairomm`
  - `glib2`
  - `glibc`
  - `glibmm`
  - `gtk3` — Debian libgtk-3-dev / Fedora gtk3-devel
  - `gtkmm3`
  - `hicolor-icon-theme` — hicolor-icon-theme (any distro)
  - `intltool`
  - `itstool`
  - `libatkmm-1.6.so`
  - `libcairomm-1.0.so`
  - `libgcc`
  - `libgcc_s.so`
  - `libgdk-3.so`
  - `libglib-2.0.so`
  - `libglibmm-2.4.so`
  - `libgtk-3.so`
  - `libpangomm-1.4.so`
  - `libsigc++`
  - `libsigc-2.0.so`
  - `libstdc++`
  - `libstdc++.so`
  - `libuuid.so`
  - `pangomm`
  - `parted`
  - `pkg-config`
  - `polkit`
  - `util-linux-libs`
  - `yelp-tools`

## Exact build steps from the recipe

```bash
cd "${srcdir}/${pkgname}-${pkgver}"

  ./configure --prefix=/usr \
      --sbindir=/usr/bin \
      --libexecdir=/usr/lib/gparted \
      --enable-online-resize \
      --enable-libparted-dmraid \
      --enable-xhost-root
  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout

Backlinks: [[forensic]] · [[gparted]] · [[Kali Gap Analysis]]
