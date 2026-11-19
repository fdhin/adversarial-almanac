---
tool: "ddrescue"
build-system: "autotools"
upstream: "https://www.gnu.org/software/ddrescue/ddrescue.html"
recipe: "arch-official/extra"
nix-package: "ddrescue"
recipe-url: "https://gitlab.archlinux.org/archlinux/packaging/packages/ddrescue/-/raw/main/PKGBUILD"
version: "1.30"
license: "unknown"
---

# Installing ddrescue

> data recovery and protection tool

Recipe source of truth: Arch official [`PKGBUILD`](https://gitlab.archlinux.org/archlinux/packaging/packages/ddrescue/-/raw/main/PKGBUILD) (version `1.30`, license n/a).

## Arch / BlackArch — prebuilt binary

```bash
sudo pacman -S ddrescue
```

## Kali / Debian-based — same package name

```bash
sudo apt install ddrescue
```

## Nix (works on any distro)

```bash
nix run nixpkgs#ddrescue    # ad-hoc run, no install
nix profile install nixpkgs#ddrescue   # or persist it
```

## Any other Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](http://www.garloff.de/kurt/linux/ddrescue/) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Exact build steps from the recipe

```bash
# fake configure script reinvents autotools and requires custom parameters to
  # set CPPFLAGS, CXXFLAGS and LDFLAGS
  local configure_options=(
    --prefix=/usr
    CPPFLAGS="$CPPFLAGS"
    CXXFLAGS="$CXXFLAGS"
    LDFLAGS="$LDFLAGS"
  )

  cd $pkgname-$pkgver
  ./configure "${configure_options[@]}"
  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout

Backlinks: [[forensic]] · [[ddrescue]] · [[Kali Gap Analysis]]
