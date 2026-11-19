---
tool: "httpry"
build-system: "make"
upstream: "http://dumpsterventures.com/jason/httpry/"
recipe: "blackarch"
aur-package: "httpry"
source: "http://dumpsterventures.com/jason/httpry/"
license: "GPL2"
blackarch-pkgver: "0.1.8"
---

# Installing httpry

> A specialized packet sniffer designed for displaying and logging HTTP traffic.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/httpry) (version `0.1.8`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S httpry    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](http://dumpsterventures.com/jason/httpry/) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `gzip`
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/man`

Backlinks: [[sniffer]] · [[httpry]] · [[BlackArch Tools]]

