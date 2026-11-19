---
tool: "dns2tcp"
build-system: "autotools"
upstream: "http://www.hsc.fr/ressources/outils/dns2tcp/index.html.en"
recipe: "blackarch"
nix-package: "dns2tcp"
source: "http://www.hsc.fr/ressources/outils/dns2tcp/index.html.en"
license: "GPL-1.0-only"
blackarch-pkgver: "0.5.2"
kali-package: "dns2tcp"
kali-version: "0.5.2-4"
---

# Installing dns2tcp

> A tool for relaying TCP connections over DNS.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/dns2tcp) (version `0.5.2`, license GPL-1.0-only).

## Arch / BlackArch

```bash
sudo pacman -S dns2tcp    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install dns2tcp    # Kali package version 0.5.2-4
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](http://www.hsc.fr/ressources/outils/dns2tcp/index.html.en) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  if [[ "$CARCH" == "i686" || "$CARCH" == "x86_64" ]];
  then
    ./configure --prefix=/usr
  else
    ./configure --build=arm --prefix=/usr
  fi

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[tunnel]] · [[dns2tcp]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#dns2tcp    # ad-hoc run, no install
nix profile install nixpkgs#dns2tcp   # or persist it
```
