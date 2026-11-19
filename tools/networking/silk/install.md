---
tool: "silk"
build-system: "autotools"
upstream: "https://tools.netsa.cert.org/silk/download.html"
recipe: "blackarch"
aur-package: "silk"
source: "https://tools.netsa.cert.org/silk/download.html"
license: "GPL-2.0-only"
blackarch-pkgver: "3.24.2"
---

# Installing silk

> A collection of traffic analysis tools developed by the CERT NetSA to facilitate security analysis of large networks.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/silk) (version `3.24.2`, license GPL-2.0-only).

## Arch / BlackArch

```bash
sudo pacman -S silk    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](https://tools.netsa.cert.org/silk/download.html) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `adns`
  - `c-ares`
  - `gnutls`
  - `libfixbuf`
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel
  - `lzo`
  - `python`
  - `snappy`

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  export LIBS="-lpthread"

  ./configure --prefix=/usr --enable-ipv6 --sbindir=/usr/bin --with-gnutls \
    --with-pic --with-pcap -with-libfixbuf --enable-output-compression \
    --enable-localtime --enable-data-rootdir="/usr/share/$pkgname"

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[networking]] · [[silk]] · [[BlackArch Tools]]

