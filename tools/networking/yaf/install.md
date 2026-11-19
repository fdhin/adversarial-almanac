---
tool: "yaf"
build-system: "autotools"
upstream: "https://tools.netsa.cert.org/yaf/download.html"
recipe: "blackarch"
aur-package: "yaf"
source: "https://tools.netsa.cert.org/yaf/download.html"
license: "GPL"
blackarch-pkgver: "2.15.0"
---

# Installing yaf

> Yet Another Flowmeter.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/yaf) (version `2.15.0`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S yaf    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](https://tools.netsa.cert.org/yaf/download.html) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `libfixbuf`
  - `libltdl`
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  ./configure --prefix=/usr --enable-applabel --enable-mpls --enable-plugins

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[networking]] · [[yaf]] · [[BlackArch Tools]]

