---
tool: "myrescue"
build-system: "make"
upstream: "http://myrescue.sourceforge.net"
recipe: "blackarch"
nix-package: "myrescue"
aur-package: "myrescue"
source: "http://myrescue.sourceforge.net"
license: "GPL"
blackarch-pkgver: "0.9.8"
kali-package: "myrescue"
kali-version: "0.9.8-4"
---

# Installing myrescue

> A hard disk recovery tool that reads undamaged regions first.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/myrescue) (version `0.9.8`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S myrescue    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install myrescue    # Kali package version 0.9.8-4
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](http://myrescue.sourceforge.net) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Exact build steps from the recipe

```bash
cd src

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/man`

Backlinks: [[forensic]] · [[myrescue]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#myrescue    # ad-hoc run, no install
nix profile install nixpkgs#myrescue   # or persist it
```
