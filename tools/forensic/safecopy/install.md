---
tool: "safecopy"
build-system: "autotools"
upstream: "https://sourceforge.net/projects/safecopy/files/safecopy/"
recipe: "blackarch"
nix-package: "safecopy"
aur-package: "safecopy"
source: "https://sourceforge.net/projects/safecopy/files/safecopy/"
license: "GPL-2.0-or-later"
blackarch-pkgver: "1.7"
kali-package: "safecopy"
kali-version: "1.7-8"
---

# Installing safecopy

> A disk data recovery tool to extract data from damaged media.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/safecopy) (version `1.7`, license GPL-2.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S safecopy    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install safecopy    # Kali package version 1.7-8
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](https://sourceforge.net/projects/safecopy/files/safecopy/) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  ./configure --prefix=/usr

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[forensic]] · [[safecopy]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#safecopy    # ad-hoc run, no install
nix profile install nixpkgs#safecopy   # or persist it
```
