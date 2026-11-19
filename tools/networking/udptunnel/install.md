---
tool: "udptunnel"
build-system: "make"
upstream: "https://code.google.com/archive/p/udptunnel/"
recipe: "blackarch"
nix-package: "udptunnel"
aur-package: "udptunnel"
source: "https://code.google.com/archive/p/udptunnel/"
license: "GPL-3.0-or-later"
blackarch-pkgver: "19"
kali-package: "udptunnel"
kali-version: "1.1-12+b1"
---

# Installing udptunnel

> Tunnels TCP over UDP packets.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/udptunnel) (version `19`, license GPL-3.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S udptunnel    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install udptunnel    # Kali package version 1.1-12+b1
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](https://code.google.com/archive/p/udptunnel/) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Exact build steps from the recipe

```bash
cd $pkgname

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[networking]] · [[udptunnel]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#udptunnel    # ad-hoc run, no install
nix profile install nixpkgs#udptunnel   # or persist it
```
