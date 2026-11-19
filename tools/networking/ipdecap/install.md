---
tool: "ipdecap"
build-system: "autotools"
upstream: "http://www.loicp.eu/ipdecap#dependances"
recipe: "blackarch"
source: "http://www.loicp.eu/ipdecap#dependances"
license: "GPL3"
blackarch-pkgver: "96.45d2a7d"
---

# Installing ipdecap

> Can decapsulate traffic encapsulated within GRE, IPIP, 6in4, ESP (ipsec) protocols, and can also remove IEEE 802.1Q (virtual lan) header.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/ipdecap) (version `96.45d2a7d`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S ipdecap    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](http://www.loicp.eu/ipdecap#dependances) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel
  - `openssl` — Debian libssl-dev / Fedora openssl-devel

## Exact build steps from the recipe

```bash
cd $pkgname

  chmod +x autogen.sh

  ./autogen.sh

  ./configure --prefix=/usr

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[networking]] · [[ipdecap]] · [[BlackArch Tools]]

