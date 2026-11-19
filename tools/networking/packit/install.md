---
tool: "packit"
build-system: "make"
upstream: "http://packetfactory.openwall.net/projects/packit/"
recipe: "blackarch"
aur-package: "packit"
source: "http://packetfactory.openwall.net/projects/packit/"
license: "GPL-2.0-or-later"
blackarch-pkgver: "2.0"
---

# Installing packit

> A network auditing tool with the ability to customize, inject, monitor, and manipulate IP traffic.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/packit) (version `2.0`, license GPL-2.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S packit    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](http://packetfactory.openwall.net/projects/packit/) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `libnet` — Debian libnet1-dev / Fedora libnet-devel
  - `libpcap` — Debian libpcap-dev / Fedora libpcap-devel

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[networking]] · [[packit]] · [[BlackArch Tools]]

