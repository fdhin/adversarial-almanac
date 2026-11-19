---
tool: "pnscan"
build-system: "autotools"
upstream: "http://www.lysator.liu.se/~pen/pnscan/"
recipe: "blackarch"
source: "http://www.lysator.liu.se/~pen/pnscan/"
license: "GPL2"
blackarch-pkgver: "1.14.1"
provides: ["t_listen"]
---

# Installing pnscan

> A parallel network scanner that can be used to survey TCP network services.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/pnscan) (version `1.14.1`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S pnscan    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](http://www.lysator.liu.se/~pen/pnscan/) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  ./configure --prefix=/usr --sbindir=/usr/bin

  make
```

## Installed layout (per recipe)

- Commands: `/usr/bin/t_listen`
- Data: `/usr/share/man`

Backlinks: [[scanner]] · [[pnscan]] · [[BlackArch Tools]]

