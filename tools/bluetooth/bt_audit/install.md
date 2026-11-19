---
tool: "bt_audit"
build-system: "make"
upstream: "http://www.betaversion.net/btdsd/download/"
recipe: "blackarch"
source: "http://www.betaversion.net/btdsd/download/"
license: "GPL"
blackarch-pkgver: "0.1.1"
provides: ["psm_scan", "rfcomm_scan"]
---

# Installing bt_audit

> Bluetooth audit

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/bt_audit) (version `0.1.1`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S bt_audit    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs gcc + make):**

**Source:** download the release from the [upstream site](http://www.betaversion.net/btdsd/download/) (the recipe uses a versioned archive).

```bash
make -j$(nproc)
sudo make install    # if no install target, copy the binary to /usr/local/bin
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `bluez-libs` — Debian libbluetooth-dev / Fedora bluez-libs-devel

## Exact build steps from the recipe

```bash
cd "$pkgname/src"

  make
```

## Installed layout (per recipe)

- Commands: `/usr/bin/psm_scan`, `/usr/bin/rfcomm_scan`

Backlinks: [[bluetooth]] · [[bt_audit]] · [[BlackArch Tools]]

