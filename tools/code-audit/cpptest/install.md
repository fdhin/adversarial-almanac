---
tool: "cpptest"
build-system: "autotools"
upstream: "https://github.com/cpptest/cpptest/releases"
recipe: "blackarch"
nix-package: "cpptest"
aur-package: "cpptest"
source: "https://github.com/cpptest/cpptest/releases"
license: "GPL"
blackarch-pkgver: "2.0.0"
---

# Installing cpptest

> A portable and powerful, yet simple, unit testing framework for handling automated tests in C++.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/cpptest) (version `2.0.0`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S cpptest    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](https://github.com/cpptest/cpptest/releases) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `gcc-libs`

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  ./configure --prefix=/usr

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[code-audit]] · [[cpptest]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#cpptest    # ad-hoc run, no install
nix profile install nixpkgs#cpptest   # or persist it
```
