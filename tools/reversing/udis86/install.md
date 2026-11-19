---
tool: "udis86"
build-system: "autotools"
upstream: "http://udis86.sourceforge.net/"
recipe: "blackarch"
nix-package: "udis86"
aur-package: "udis86"
source: "http://udis86.sourceforge.net/"
license: "BSD"
blackarch-pkgver: "1.7.2"
---

# Installing udis86

> A minimalistic disassembler library.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/udis86) (version `1.7.2`, license BSD).

## Arch / BlackArch

```bash
sudo pacman -S udis86    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](http://udis86.sourceforge.net/) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `python`

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  ./configure --enable-shared --prefix=/usr

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/docs`

Backlinks: [[reversing]] · [[udis86]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#udis86    # ad-hoc run, no install
nix profile install nixpkgs#udis86   # or persist it
```
