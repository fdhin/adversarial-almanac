---
tool: "bluefog"
build-system: "make"
upstream: ""
recipe: "blackarch"
source: ""
license: "GPL2"
blackarch-pkgver: "0.0.4"
---

# Installing bluefog

> A tool that can generate an essentially unlimited number of phantom Bluetooth devices.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/bluefog) (version `0.0.4`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S bluefog    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the the upstream site (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `bluez`
  - `bluez-libs` — Debian libbluetooth-dev / Fedora bluez-libs-devel

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  make
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[bluetooth]] · [[bluefog]] · [[BlackArch Tools]]

