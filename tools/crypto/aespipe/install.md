---
tool: "aespipe"
build-system: "autotools"
upstream: "http://loop-aes.sourceforge.net/aespipe/"
recipe: "blackarch"
nix-package: "aespipe"
aur-package: "aespipe"
source: "http://loop-aes.sourceforge.net/aespipe/"
license: "GPL"
blackarch-pkgver: "2.4i"
---

# Installing aespipe

> Reads data from stdin and outputs encrypted or decrypted results to stdout.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/aespipe) (version `2.4i`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S aespipe    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](http://loop-aes.sourceforge.net/aespipe/) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Exact build steps from the recipe

```bash
cd "$pkgname-v$pkgver"

  ./configure --prefix=/usr

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[crypto]] · [[aespipe]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#aespipe    # ad-hoc run, no install
nix profile install nixpkgs#aespipe   # or persist it
```
