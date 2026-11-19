---
tool: "lsrtunnel"
build-system: "autotools"
upstream: "http://www.synacklabs.net/projects/lsrtunnel/"
recipe: "blackarch"
source: "http://www.synacklabs.net/projects/lsrtunnel/"
license: "GPL"
blackarch-pkgver: "0.2"
---

# Installing lsrtunnel

> Spoofs connections using source routed packets.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/lsrtunnel) (version `0.2`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S lsrtunnel    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](http://www.synacklabs.net/projects/lsrtunnel/) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `libdnet` — Debian libdumbnet-dev / Fedora libdnet-devel
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  ./configure --prefix=/usr --mandir=/usr/share/man

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[spoof]] · [[lsrtunnel]] · [[BlackArch Tools]]

