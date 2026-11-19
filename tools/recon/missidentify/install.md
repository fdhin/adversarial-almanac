---
tool: "missidentify"
build-system: "autotools"
upstream: "https://missidentify.sourceforge.net/"
recipe: "blackarch"
nix-package: "missidentify"
source: "https://missidentify.sourceforge.net/"
license: "GPL-2.0-or-later"
blackarch-pkgver: "1.0"
kali-package: "missidentify"
kali-version: "1.0-12"
---

# Installing missidentify

> A program to find Win32 applications.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/missidentify) (version `1.0`, license GPL-2.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S missidentify    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install missidentify    # Kali package version 1.0-12
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](https://missidentify.sourceforge.net/) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  if [[ "$CARCH" == "i686" || "$CARCH" == "x86_64" ]]; then
    ./configure --prefix=/usr
  else
    ./configure --prefix=/usr --build=arm
  fi

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[missidentify]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#missidentify    # ad-hoc run, no install
nix profile install nixpkgs#missidentify   # or persist it
```
