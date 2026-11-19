---
tool: "airpwn"
build-system: "autotools"
upstream: "http://airpwn.sourceforge.net/"
recipe: "blackarch"
source: "http://airpwn.sourceforge.net/"
license: "GPL"
blackarch-pkgver: "1.4"
---

# Installing airpwn

> A tool for generic packet injection on an 802.11 network.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/airpwn) (version `1.4`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S airpwn    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](http://airpwn.sourceforge.net) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `libnet` — Debian libnet1-dev / Fedora libnet-devel
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel
  - `lorcon-old`
  - `openssl` — Debian libssl-dev / Fedora openssl-devel
  - `pcre`
  - `python2`
  - `wireless_tools` — Debian wireless-tools / Fedora wireless-tools

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  unset LDFLAGS

  ./configure --prefix=/usr --mandir=/usr/share/man

  sed -i 's/python2.4/python2/g' conf.h
  sed -i 's|python2/Python\.h|python2.7/Python.h|' conf.h
  sed -i 's|LDFLAGS = |LDFLAGS = -lssl -lcrypto -lorcon -lpthread -lpcre -lpcap -lnet -lpython2.7|g' Makefile

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[wireless]] · [[airpwn]] · [[BlackArch Tools]]

