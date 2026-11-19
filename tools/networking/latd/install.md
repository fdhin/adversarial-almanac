---
tool: "latd"
build-system: "autotools"
upstream: "http://sourceforge.net/projects/linux-decnet/files/latd/1.31/"
recipe: "blackarch"
source: "http://sourceforge.net/projects/linux-decnet/files/latd/1.31/"
license: "GPL2"
blackarch-pkgver: "1.31"
---

# Installing latd

> A LAT terminal daemon for Linux and BSD.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/latd) (version `1.31`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S latd    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](http://sourceforge.net/projects/linux-decnet/files/latd/1.31/) (the recipe uses a versioned archive).

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

Backlinks: [[networking]] · [[latd]] · [[BlackArch Tools]]

