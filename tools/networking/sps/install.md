---
tool: "sps"
build-system: "make"
upstream: "https://sites.google.com/site/simplepacketsender/"
recipe: "blackarch"
aur-package: "sps"
source: "https://sites.google.com/site/simplepacketsender/"
license: "GPL-3.0-or-later"
blackarch-pkgver: "4.3"
---

# Installing sps

> A Linux packet crafting tool. Supports IPv4, IPv6 including extension headers, and tunneling IPv6 over IPv4.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/sps) (version `4.3`, license GPL-3.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S sps    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](https://sites.google.com/site/simplepacketsender/) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `gtk2`
  - `gtk3` — Debian libgtk-3-dev / Fedora gtk3-devel

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  case $CARCH in
    "x86_64") cd "$srcdir/sps-$pkgver/src64" ;;
    *) cd "$srcdir/sps-$pkgver/src32"
  esac

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/sps`

Backlinks: [[networking]] · [[sps]] · [[BlackArch Tools]]

