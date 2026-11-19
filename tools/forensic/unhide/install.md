---
tool: "unhide"
build-system: "make"
upstream: "https://github.com/YJesus/Unhide/"
recipe: "arch-official/extra"
nix-package: "unhide"
recipe-url: "https://gitlab.archlinux.org/archlinux/packaging/packages/unhide/-/raw/main/PKGBUILD"
version: "20240510"
license: "GPL3"
provides: ["unhide"]
---

# Installing unhide

> forensic tool to find hidden processes and ports

Recipe source of truth: Arch official [`PKGBUILD`](https://gitlab.archlinux.org/archlinux/packaging/packages/unhide/-/raw/main/PKGBUILD) (version `20240510`, license GPL3).

## Arch / BlackArch — prebuilt binary

```bash
sudo pacman -S unhide
```

## Kali / Debian-based — same package name

```bash
sudo apt install unhide
```

## Nix (works on any distro)

```bash
nix run nixpkgs#unhide    # ad-hoc run, no install
nix profile install nixpkgs#unhide   # or persist it
```

## Any other Linux distro

**From source (needs gcc + make):**

```bash
git clone https://github.com/YJesus/Unhide/.git
cd unhide
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `iproute2`
  - `lsof`
  - `net-tools`
  - `procps-ng`
  - `psmisc`

## Exact build steps from the recipe

```bash
cd Unhide-$pkgver
  make
```

## Installed layout (per recipe)

- Commands: `/usr/bin/unhide`

Backlinks: [[forensic]] · [[unhide]] · [[Kali Gap Analysis]]
