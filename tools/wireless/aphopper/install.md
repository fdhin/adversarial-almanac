---
tool: "aphopper"
build-system: "autotools"
upstream: "http://aphopper.sourceforge.net/"
recipe: "blackarch"
source: "http://aphopper.sourceforge.net/"
license: "GPL"
blackarch-pkgver: "0.3"
---

# Installing aphopper

> A program that automatically hops between access points of different wireless networks.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/aphopper) (version `0.3`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S aphopper    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](http://aphopper.sourceforge.net/) (the recipe uses a versioned archive).

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

  CPPFLAGS= ./configure --prefix=/usr

  make CC="gcc $CFLAGS"
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[wireless]] · [[aphopper]] · [[BlackArch Tools]]

