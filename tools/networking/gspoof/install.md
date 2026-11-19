---
tool: "gspoof"
build-system: "autotools"
upstream: "http://gspoof.sourceforge.net/"
recipe: "blackarch"
source: "http://gspoof.sourceforge.net/"
license: "MIT"
blackarch-pkgver: "3.2"
---

# Installing gspoof

> A simple GTK/command line TCP/IP packet generator.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/gspoof) (version `3.2`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S gspoof    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](http://gspoof.sourceforge.net/) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **Build tools**: `autoconf`, `automake`, `make`
- **System libraries** (Arch name — translate for your distro):
  - `glib2`
  - `gtk2`
  - `libnet` — Debian libnet1-dev / Fedora libnet-devel

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  ./configure --prefix=/usr

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/pixmaps`

Backlinks: [[networking]] · [[gspoof]] · [[BlackArch Tools]]

