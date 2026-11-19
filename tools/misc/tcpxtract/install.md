---
tool: "tcpxtract"
build-system: "autotools"
upstream: "http://tcpxtract.sourceforge.net"
recipe: "blackarch"
aur-package: "tcpxtract"
source: "http://tcpxtract.sourceforge.net"
license: "GPL"
blackarch-pkgver: "1.0.1"
---

# Installing tcpxtract

> A tool for extracting files from network traffic.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/tcpxtract) (version `1.0.1`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S tcpxtract    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](http://tcpxtract.sourceforge.net) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `flex`
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  if [[ "$CARCH" == "i686" || "$CARCH" == "x86_64" ]];
  then
    ./configure --prefix=/usr --sysconfdir=/etc
  else
    ./configure --build=arm --prefix=/usr --sysconfdir=/etc
  fi

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[misc]] · [[tcpxtract]] · [[BlackArch Tools]]

