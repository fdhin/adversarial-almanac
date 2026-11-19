---
tool: "dhcdrop"
build-system: "autotools"
upstream: "http://www.netpatch.ru/dhcdrop.html"
recipe: "blackarch"
source: "http://www.netpatch.ru/dhcdrop.html"
license: "GPL"
blackarch-pkgver: "0.5"
---

# Installing dhcdrop

> Remove illegal dhcp servers with IP-pool underflow.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/dhcdrop) (version `0.5`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S dhcdrop    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](http://www.netpatch.ru/dhcdrop.html) (the recipe uses a versioned archive).

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

Backlinks: [[misc]] · [[dhcdrop]] · [[BlackArch Tools]]

