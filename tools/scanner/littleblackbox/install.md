---
tool: "littleblackbox"
build-system: "autotools"
upstream: "http://code.google.com/p/littleblackbox/wiki/FAQ"
recipe: "blackarch"
source: "http://code.google.com/p/littleblackbox/wiki/FAQ"
license: "MIT"
blackarch-pkgver: "0.1.3"
provides: ["lbb.db"]
---

# Installing littleblackbox

> Penetration testing tool, search in a collection of thousands of private SSL keys extracted from various embedded devices.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/littleblackbox) (version `0.1.3`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S littleblackbox    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](http://code.google.com/p/littleblackbox/wiki/FAQ) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `curl`
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel
  - `openssl-1.0`
  - `sqlite` — Debian libsqlite3-dev / Fedora sqlite-devel
  - `tar`

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  rm -f bin/littleblackbox 2>/dev/null

  cd src

  make distclean

  ./configure --prefix=/usr

  make
```

## Installed layout (per recipe)

- Commands: `/usr/bin/lbb.db`

Backlinks: [[scanner]] · [[littleblackbox]] · [[BlackArch Tools]]

