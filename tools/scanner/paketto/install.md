---
tool: "paketto"
build-system: "autotools"
upstream: "http://www.doxpara.com/paketto/"
recipe: "blackarch"
source: "http://www.doxpara.com/paketto/"
license: "BSD-3-Clause"
blackarch-pkgver: "3.0"
---

# Installing paketto

> Advanced TCP/IP Toolkit.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/paketto) (version `3.0`, license BSD-3-Clause).

## Arch / BlackArch

```bash
sudo pacman -S paketto    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](http://www.doxpara.com/paketto/) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `glibc`
  - `libghthash`
  - `libnet` — Debian libnet1-dev / Fedora libnet-devel
  - `libnetfilter_queue`
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel
  - `libtomcrypt`

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  autoreconf -i

  if [[ "$CARCH" == "i686" || "$CARCH" == "x86_64" ]]; then
    ./configure --prefix=/usr --mandir=/usr/share/man --sbindir=/usr/bin
  else
    ./configure --build=arm --prefix=/usr --mandir=/usr/share/man \
      --sbindir=/usr/bin
  fi

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[paketto]] · [[BlackArch Tools]]

