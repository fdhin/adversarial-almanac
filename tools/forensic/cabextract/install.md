---
tool: "cabextract"
build-system: "autotools"
upstream: "https://www.cabextract.org.uk/"
recipe: "arch-official/extra"
nix-package: "cabextract"
recipe-url: "https://gitlab.archlinux.org/archlinux/packaging/packages/cabextract/-/raw/main/PKGBUILD"
version: "1.11"
license: "unknown"
---

# Installing cabextract

> Microsoft Cabinet file unpacker

Recipe source of truth: Arch official [`PKGBUILD`](https://gitlab.archlinux.org/archlinux/packaging/packages/cabextract/-/raw/main/PKGBUILD) (version `1.11`, license n/a).

## Arch / BlackArch — prebuilt binary

```bash
sudo pacman -S cabextract
```

## Kali / Debian-based — same package name

```bash
sudo apt install cabextract
```

## Nix (works on any distro)

```bash
nix run nixpkgs#cabextract    # ad-hoc run, no install
nix profile install nixpkgs#cabextract   # or persist it
```

## Any other Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](https://www.cabextract.org.uk/) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Exact build steps from the recipe

```bash
cd libmspack/cabextract
  ./configure \
    --prefix=/usr \
    --sysconfdir=/etc \
    --mandir=/usr/share/man
  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout

Backlinks: [[forensic]] · [[cabextract]] · [[Kali Gap Analysis]]
