---
tool: "pdfcrack"
build-system: "make"
upstream: "https://pdfcrack.sourceforge.net/"
recipe: "arch-official/extra"
nix-package: "pdfcrack"
recipe-url: "https://gitlab.archlinux.org/archlinux/packaging/packages/pdfcrack/-/raw/main/PKGBUILD"
version: "0.21"
license: "GPL-2.0-or-later"
---

# Installing pdfcrack

> PDF files password cracker

Recipe source of truth: Arch official [`PKGBUILD`](https://gitlab.archlinux.org/archlinux/packaging/packages/pdfcrack/-/raw/main/PKGBUILD) (version `0.21`, license GPL-2.0-or-later).

## Arch / BlackArch — prebuilt binary

```bash
sudo pacman -S pdfcrack
```

## Kali / Debian-based — same package name

```bash
sudo apt install pdfcrack
```

## Nix (works on any distro)

```bash
nix run nixpkgs#pdfcrack    # ad-hoc run, no install
nix profile install nixpkgs#pdfcrack   # or persist it
```

## Any other Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](http://pdfcrack.sf.net) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `glibc`

## Exact build steps from the recipe

```bash
cd ${pkgname}-${pkgver}
  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout

Backlinks: [[cracker]] · [[pdfcrack]] · [[Kali Gap Analysis]]
