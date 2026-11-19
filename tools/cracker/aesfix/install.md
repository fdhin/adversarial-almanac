---
tool: "aesfix"
build-system: "make"
upstream: "http://citp.princeton.edu/memory/code/"
recipe: "blackarch"
nix-package: "aesfix"
aur-package: "aesfix"
source: "http://citp.princeton.edu/memory/code/"
license: "BSD"
blackarch-pkgver: "1.0.1"
kali-package: "aesfix"
kali-version: "1.0.1-8"
---

# Installing aesfix

> A tool to find AES key in RAM.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/aesfix) (version `1.0.1`, license BSD).

## Arch / BlackArch

```bash
sudo pacman -S aesfix    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install aesfix    # Kali package version 1.0.1-8
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](http://citp.princeton.edu/memory/code/) (the recipe uses a versioned archive).

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

Backlinks: [[cracker]] · [[aesfix]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#aesfix    # ad-hoc run, no install
nix profile install nixpkgs#aesfix   # or persist it
```
