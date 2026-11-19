---
tool: "aeskeyfind"
build-system: "make"
upstream: "http://citp.princeton.edu/memory/code/"
recipe: "blackarch"
nix-package: "aeskeyfind"
aur-package: "aeskeyfind"
source: "http://citp.princeton.edu/memory/code/"
license: "BSD"
blackarch-pkgver: "1.0"
kali-package: "aeskeyfind"
kali-version: "1:1.0-11"
---

# Installing aeskeyfind

> A tool to find AES key in RAM.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/aeskeyfind) (version `1.0`, license BSD).

## Arch / BlackArch

```bash
sudo pacman -S aeskeyfind    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install aeskeyfind    # Kali package version 1:1.0-11
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

Backlinks: [[cracker]] · [[aeskeyfind]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#aeskeyfind    # ad-hoc run, no install
nix profile install nixpkgs#aeskeyfind   # or persist it
```
