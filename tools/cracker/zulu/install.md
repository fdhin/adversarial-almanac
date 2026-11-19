---
tool: "zulu"
build-system: "autotools"
upstream: "http://sourceforge.net/projects/zulu-wireless/"
recipe: "blackarch"
nix-package: "zulu"
source: "http://sourceforge.net/projects/zulu-wireless/"
license: "GPL"
blackarch-pkgver: "0.1"
---

# Installing zulu

> A light weight 802.11 wireless frame generation tool to enable fast and easy debugging and probing of 802.11 networks.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/zulu) (version `0.1`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S zulu    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](http://sourceforge.net/projects/zulu-wireless/) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `glibc`

## Exact build steps from the recipe

```bash
cd $pkgname

  ./configure --prefix=/usr

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[cracker]] · [[zulu]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#zulu    # ad-hoc run, no install
nix profile install nixpkgs#zulu   # or persist it
```
