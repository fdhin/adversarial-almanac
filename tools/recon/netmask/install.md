---
tool: "netmask"
build-system: "autotools"
upstream: "http://packages.qa.debian.org/n/netmask.html"
recipe: "blackarch"
nix-package: "netmask"
aur-package: "netmask"
source: "http://packages.qa.debian.org/n/netmask.html"
license: "GPL2"
blackarch-pkgver: "2.4.4"
kali-package: "netmask"
kali-version: "2.5.0-1"
---

# Installing netmask

> Helps determine network masks

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/netmask) (version `2.4.4`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S netmask    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install netmask    # Kali package version 2.5.0-1
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](http://packages.qa.debian.org/n/netmask.html) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  ./autogen

  ./configure --prefix=/usr --mandir=/usr/share/man

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[netmask]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#netmask    # ad-hoc run, no install
nix profile install nixpkgs#netmask   # or persist it
```
