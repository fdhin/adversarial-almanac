---
tool: "bonesi"
build-system: "autotools"
upstream: "https://github.com/Markus-Go/bonesi"
recipe: "blackarch"
source: "https://github.com/Markus-Go/bonesi"
license: "Apache2"
blackarch-pkgver: "18.40317aa"
---

# Installing bonesi

> The DDoS Botnet Simulator.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/bonesi) (version `18.40317aa`, license Apache2).

## Arch / BlackArch

```bash
sudo pacman -S bonesi    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs compiler + make):**

**Source:** download the release from the [upstream site](https://github.com/Markus-Go/bonesi) (the recipe uses a versioned archive).

```bash
./configure
make -j$(nproc)
sudo make install
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `libnet` — Debian libnet1-dev / Fedora libnet-devel
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel

## Exact build steps from the recipe

```bash
cd $pkgname

  ./configure --prefix=/usr
  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/man`

Backlinks: [[dos]] · [[bonesi]] · [[BlackArch Tools]]

