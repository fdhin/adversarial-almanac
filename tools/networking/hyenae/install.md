---
tool: "hyenae"
build-system: "autotools"
upstream: "http://sourceforge.net/projects/hyenae/"
recipe: "blackarch"
aur-package: "hyenae"
source: "http://sourceforge.net/projects/hyenae/"
license: "GPL"
blackarch-pkgver: "0.36_1"
---

# Installing hyenae

> Flexible platform independent packet generator.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/hyenae) (version `0.36_1`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S hyenae    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](http://sourceforge.net/projects/hyenae/) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `libdnet` — Debian libdumbnet-dev / Fedora libdnet-devel
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel

## Exact build steps from the recipe

```bash
cd "$pkgname-${pkgver/_/-}"

  ./configure --prefix=/usr

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[networking]] · [[hyenae]] · [[BlackArch Tools]]

