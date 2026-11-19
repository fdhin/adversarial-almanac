---
tool: "honeyd"
build-system: "autotools"
upstream: "https://github.com/DataSoft/Honeyd/"
recipe: "blackarch"
source: "https://github.com/DataSoft/Honeyd/"
license: "GPL"
blackarch-pkgver: "337.a0f3d64"
---

# Installing honeyd

> A small daemon that creates virtual hosts on a network.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/honeyd) (version `337.a0f3d64`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S honeyd    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + make):**

```bash
git clone https://github.com/DataSoft/Honeyd.git
cd Honeyd
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `libdnet` — Debian libdumbnet-dev / Fedora libdnet-devel
  - `libedit`
  - `libevent`
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel
  - `pcre`
  - `python2`
  - `tcl`
  - `zlib` — Debian zlib1g-dev / Fedora zlib-devel

## Exact build steps from the recipe

```bash
cd $pkgname

  ./autogen.sh

  ./configure --prefix=/usr

  #patch -p1 -i ../python.patch

  make

  sed -i 's/python/python2/' Makefile
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[honeypot]] · [[honeyd]] · [[BlackArch Tools]]

