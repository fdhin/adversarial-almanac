---
tool: "kismet"
build-system: "autotools"
upstream: "https://www.kismetwireless.net/"
recipe: "arch-official/extra"
nix-package: "kismet"
recipe-url: "https://gitlab.archlinux.org/archlinux/packaging/packages/kismet/-/raw/main/PKGBUILD"
version: "2025_09_R1"
license: "unknown"
---

# Installing kismet

> wireless network and device detector (metapackage)

Recipe source of truth: Arch official [`PKGBUILD`](https://gitlab.archlinux.org/archlinux/packaging/packages/kismet/-/raw/main/PKGBUILD) (version `2025_09_R1`, license n/a).

## Arch / BlackArch — prebuilt binary

```bash
sudo pacman -S kismet
```

## Kali / Debian-based — same package name

```bash
sudo apt install kismet
```

## Nix (works on any distro)

```bash
nix run nixpkgs#kismet    # ad-hoc run, no install
nix profile install nixpkgs#kismet   # or persist it
```

## Any other Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](https://www.kismetwireless.net/) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Exact build steps from the recipe

```bash
cd ${pkgname}-${_realver}
  ./configure \
    --prefix=/usr \
    --localstatedir=/var \
    --sysconfdir=/etc/kismet \
    --with-suidgroup=315
  make all plugins
```

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout

Backlinks: [[wireless]] · [[kismet]] · [[Kali Gap Analysis]]
