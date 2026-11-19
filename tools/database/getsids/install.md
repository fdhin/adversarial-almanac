---
tool: "getsids"
build-system: "autotools"
upstream: "http://www.cqure.net/wp/getsids/"
recipe: "blackarch"
source: "http://www.cqure.net/wp/getsids/"
license: "GPL-2.0-or-later"
blackarch-pkgver: "1.0.0"
---

# Installing getsids

> Enumerate Oracle Sids by sending the services command to the Oracle TNS listener.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/getsids) (version `1.0.0`, license GPL-2.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S getsids    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](http://www.cqure.net/wp/getsids/) (the recipe uses a versioned archive).

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
cd "$pkgname-$pkgver"

  autoreconf -i
  ./configure --prefix=/usr
  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[database]] · [[getsids]] · [[BlackArch Tools]]

