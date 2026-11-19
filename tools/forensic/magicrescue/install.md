---
tool: "magicrescue"
build-system: "autotools"
upstream: "https://github.com/jbj/magicrescue"
recipe: "blackarch"
nix-package: "magicrescue"
aur-package: "magicrescue"
source: "https://github.com/jbj/magicrescue"
license: "GPL-1.0-or-later"
blackarch-pkgver: "v1.1.10.r8.gd9a5793"
kali-package: "magicrescue"
kali-version: "1.1.10+dfsg-3"
---

# Installing magicrescue

> Find and recover deleted files on block devices.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/magicrescue) (version `v1.1.10.r8.gd9a5793`, license GPL-1.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S magicrescue    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install magicrescue    # Kali package version 1.1.10+dfsg-3
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](https://github.com/jbj/magicrescue) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **Build tools**: `git`

## Exact build steps from the recipe

```bash
cd $pkgname

  ./configure --prefix=/usr

  make PREFIX="$pkgdir/usr"
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[forensic]] · [[magicrescue]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#magicrescue    # ad-hoc run, no install
nix profile install nixpkgs#magicrescue   # or persist it
```
