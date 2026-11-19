---
tool: "bluescan"
build-system: "scripts"
upstream: "http://www.darknet.org.uk/2015/01/bluescan-bluetooth-device-scanner/"
recipe: "blackarch"
source: "http://www.darknet.org.uk/2015/01/bluescan-bluetooth-device-scanner/"
license: "custom:unknown"
blackarch-pkgver: "1.0.6"
---

# Installing bluescan

> A Bluetooth Device Scanner.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/bluescan) (version `1.0.6`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S bluescan    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://www.darknet.org.uk/2015/01/bluescan-bluetooth-device-scanner/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `bash`
  - `bluez`
  - `bluez-hciconfig`
  - `bluez-libs` — Debian libbluetooth-dev / Fedora bluez-libs-devel
  - `bluez-utils`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[bluetooth]] · [[bluescan]] · [[BlackArch Tools]]

