---
tool: "minicom"
build-system: "autotools"
upstream: "https://salsa.debian.org/minicom-team/minicom"
recipe: "arch-official/extra"
nix-package: "minicom"
recipe-url: "https://gitlab.archlinux.org/archlinux/packaging/packages/minicom/-/raw/main/PKGBUILD"
version: "2.11.1"
license: "GPL-2.0-or-later"
---

# Installing minicom

> menu-driven serial communication program

Recipe source of truth: Arch official [`PKGBUILD`](https://gitlab.archlinux.org/archlinux/packaging/packages/minicom/-/raw/main/PKGBUILD) (version `2.11.1`, license GPL-2.0-or-later).

## Arch / BlackArch — prebuilt binary

```bash
sudo pacman -S minicom
```

## Kali / Debian-based — same package name

```bash
sudo apt install minicom
```

## Nix (works on any distro)

```bash
nix run nixpkgs#minicom    # ad-hoc run, no install
nix profile install nixpkgs#minicom   # or persist it
```

## Any other Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](https://salsa.debian.org/minicom-team/minicom) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `bash`
  - `glibc`
  - `libncursesw.so`
  - `ncurses`

## Exact build steps from the recipe

```bash
cd "${pkgname}-${pkgver}"

  ./configure \
     --prefix=/usr \
     --sysconfdir=/etc
  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout

Backlinks: [[hardware]] · [[minicom]] · [[Kali Gap Analysis]]
