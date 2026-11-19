---
tool: "pixd"
build-system: "make"
upstream: "https://github.com/FireyFly/pixd"
recipe: "blackarch"
nix-package: "pixd"
aur-package: "pixd"
source: "https://github.com/FireyFly/pixd"
license: "MIT"
blackarch-pkgver: "9.f49add4"
---

# Installing pixd

> Colourful visualization tool for binary files.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/pixd) (version `9.f49add4`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S pixd    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](https://github.com/FireyFly/pixd) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **Build tools**: `git`

## Exact build steps from the recipe

```bash
cd $pkgname

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/man`

Backlinks: [[binary]] · [[pixd]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#pixd    # ad-hoc run, no install
nix profile install nixpkgs#pixd   # or persist it
```
