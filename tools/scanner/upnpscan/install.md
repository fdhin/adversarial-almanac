---
tool: "upnpscan"
build-system: "autotools"
upstream: "http://www.cqure.net/wp/upnpscan/"
recipe: "blackarch"
source: "http://www.cqure.net/wp/upnpscan/"
license: "GPL3"
blackarch-pkgver: "0.4"
---

# Installing upnpscan

> Scans the LAN or a given address range for UPnP capable devices.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/upnpscan) (version `0.4`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S upnpscan    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](http://www.cqure.net/wp/upnpscan/) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **Build tools**: `autoconf`
- **System libraries** (Arch name — translate for your distro):
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel
  - `libtool`

## Exact build steps from the recipe

```bash
cd $pkgname

  libtoolize --copy --force -i
  aclocal
  autoconf
  automake --add-missing

  ./configure --prefix=/usr

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[upnpscan]] · [[BlackArch Tools]]

