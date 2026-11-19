---
tool: "bittwist"
build-system: "make"
upstream: "http://bittwist.sourceforge.net/"
recipe: "blackarch"
aur-package: "bittwist"
source: "http://bittwist.sourceforge.net/"
license: "GPL"
blackarch-pkgver: "2.0"
---

# Installing bittwist

> A simple yet powerful libpcap-based Ethernet packet generator. It is designed to complement tcpdump, which by itself has done a great job at capturing network traffic.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/bittwist) (version `2.0`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S bittwist    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](http://bittwist.sourceforge.net/) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel

## Exact build steps from the recipe

```bash
cd "$pkgname-linux-$pkgver"

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[sniffer]] · [[bittwist]] · [[BlackArch Tools]]

