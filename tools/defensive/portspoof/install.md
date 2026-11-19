---
tool: "portspoof"
build-system: "autotools"
upstream: "https://drk1wi.github.io/portspoof/"
recipe: "blackarch"
source: "https://drk1wi.github.io/portspoof/"
license: "GPL2"
blackarch-pkgver: "162.6618156"
---

# Installing portspoof

> This program's primary goal is to enhance OS security through a set of new techniques.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/portspoof) (version `162.6618156`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S portspoof    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](https://drk1wi.github.io/portspoof/) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **Build tools**: `automake`, `git`
- **System libraries** (Arch name — translate for your distro):
  - `gcc-libs`

## Exact build steps from the recipe

```bash
cd $pkgname

  ./configure --prefix=/usr

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[defensive]] · [[portspoof]] · [[BlackArch Tools]]

