---
tool: "dmitry"
build-system: "autotools"
upstream: "http://www.mor-pah.net/"
recipe: "blackarch"
nix-package: "dmitry"
aur-package: "dmitry"
source: "http://www.mor-pah.net/"
license: "GPL"
blackarch-pkgver: "1.3a"
kali-package: "dmitry"
kali-version: "1.3a-8"
---

# Installing dmitry

> Deepmagic Information Gathering Tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/dmitry) (version `1.3a`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S dmitry    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install dmitry    # Kali package version 1.3a-8
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](http://www.mor-pah.net/) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Exact build steps from the recipe

```bash
cd "$_pkgname-$pkgver"

  export CFLAGS="-fcommon"

  ./configure --prefix=/usr --sysconfdir=/etc

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/man`

Backlinks: [[scanner]] · [[dmitry]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#dmitry    # ad-hoc run, no install
nix profile install nixpkgs#dmitry   # or persist it
```
