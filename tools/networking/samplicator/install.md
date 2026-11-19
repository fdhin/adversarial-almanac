---
tool: "samplicator"
build-system: "autotools"
upstream: "https://github.com/sleinen/samplicator"
recipe: "blackarch"
nix-package: "samplicator"
source: "https://github.com/sleinen/samplicator"
license: "GPL2"
blackarch-pkgver: "175.ceeb1d2"
---

# Installing samplicator

> Send copies of (UDP) datagrams to multiple receivers, with optional sampling and spoofing.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/samplicator) (version `175.ceeb1d2`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S samplicator    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](https://github.com/sleinen/samplicator) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **Build tools**: `git`

## Exact build steps from the recipe

```bash
cd $pkgname

  ./autogen.sh

  ./configure --prefix=/usr

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[networking]] · [[samplicator]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#samplicator    # ad-hoc run, no install
nix profile install nixpkgs#samplicator   # or persist it
```
