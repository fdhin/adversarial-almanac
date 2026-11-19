---
tool: "dbd"
build-system: "make"
upstream: "https://github.com/gitdurandal/dbd"
recipe: "blackarch"
nix-package: "dbd"
source: "https://github.com/gitdurandal/dbd"
license: "GPL3"
blackarch-pkgver: "61.8cf5350"
kali-package: "dbd"
kali-version: "1.50-1kali7"
---

# Installing dbd

> A Netcat-clone, designed to be portable and offer strong encryption. It runs on Unix-like operating systems and on Microsoft Win32.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/dbd) (version `61.8cf5350`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S dbd    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install dbd    # Kali package version 1.50-1kali7
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](https://github.com/gitdurandal/dbd) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **Build tools**: `git`

## Exact build steps from the recipe

```bash
cd $pkgname

  make unix
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[misc]] · [[dbd]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#dbd    # ad-hoc run, no install
nix profile install nixpkgs#dbd   # or persist it
```
