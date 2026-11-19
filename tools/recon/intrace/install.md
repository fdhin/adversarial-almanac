---
tool: "intrace"
build-system: "make"
upstream: "http://intrace.googlecode.com"
recipe: "blackarch"
source: "http://intrace.googlecode.com"
license: "GPL-2.0-only, GPL-3.0-only"
blackarch-pkgver: "1.6"
kali-package: "intrace"
kali-version: "1.6-0kali2"
---

# Installing intrace

> Traceroute-like application piggybacking on existing TCP connections.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/intrace) (version `1.6`, license GPL-2.0-only, GPL-3.0-only).

## Arch / BlackArch

```bash
sudo pacman -S intrace    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install intrace    # Kali package version 1.6-0kali2
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](http://intrace.googlecode.com) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgname-$pkgver"

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[intrace]] · [[BlackArch Tools]]

