---
tool: "scanssh"
build-system: "autotools"
upstream: "http://www.monkey.org/~provos/scanssh/"
recipe: "blackarch"
aur-package: "scanssh"
source: "http://www.monkey.org/~provos/scanssh/"
license: "GPL-1.0-or-later"
blackarch-pkgver: "2.1"
---

# Installing scanssh

> Fast SSH server and open proxy scanner.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/scanssh) (version `2.1`, license GPL-1.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S scanssh    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](http://www.monkey.org/~provos/scanssh/) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `glibc`
  - `libdnet` — Debian libdumbnet-dev / Fedora libdnet-devel
  - `libevent`
  - `libmd`
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel
  - `openssl` — Debian libssl-dev / Fedora openssl-devel
  - `openssl-1.0`
  - `openssl-1.1`

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  autoreconf -fi

  if [[ "$CARCH" == "i686" || "$CARCH" == "x86_64" ]];
  then
    ./configure --prefix=/usr
  else
    ./configure --build=arm --prefix=/usr
  fi

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[scanssh]] · [[BlackArch Tools]]

