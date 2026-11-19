---
tool: "cflow"
build-system: "autotools"
upstream: "http://www.gnu.org/software/cflow/"
recipe: "blackarch"
nix-package: "cflow"
aur-package: "cflow"
source: "http://www.gnu.org/software/cflow/"
license: "GPL"
blackarch-pkgver: "1.8"
---

# Installing cflow

> A C program flow analyzer.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/cflow) (version `1.8`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S cflow    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](http://www.gnu.org/software/cflow/) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  ./configure --prefix=/usr

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[code-audit]] · [[cflow]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#cflow    # ad-hoc run, no install
nix profile install nixpkgs#cflow   # or persist it
```
