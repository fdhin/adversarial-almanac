---
tool: "synscan"
build-system: "autotools"
upstream: "https://www.digit-labs.org/files/tools/synscan/"
recipe: "blackarch"
source: "https://www.digit-labs.org/files/tools/synscan/"
license: "GPL-2.0-or-later"
blackarch-pkgver: "5.02"
provides: ["sslog"]
---

# Installing synscan

> Fast asynchronous half-open TCP portscanner.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/synscan) (version `5.02`, license GPL-2.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S synscan    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](https://www.digit-labs.org/files/tools/synscan/) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  ./configure --prefix=/usr

  make linux
```

## Installed layout (per recipe)

- Commands: `/usr/bin/sslog`

Backlinks: [[scanner]] · [[synscan]] · [[BlackArch Tools]]

