---
tool: "bluelog"
build-system: "scripts"
upstream: "http://www.digifail.com/software/bluelog.shtml"
recipe: "blackarch"
aur-package: "bluelog"
source: "http://www.digifail.com/software/bluelog.shtml"
license: "GPL2"
blackarch-pkgver: "1.1.2"
kali-package: "bluelog"
kali-version: "1.1.2-1kali3"
---

# Installing bluelog

> A Bluetooth scanner and sniffer written to do a single task, log devices that are in discoverable mode.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/bluelog) (version `1.1.2`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S bluelog    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install bluelog    # Kali package version 1.1.2-1kali3
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://www.digifail.com/software/bluelog.shtml) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `bluez`
  - `bluez-libs` — Debian libbluetooth-dev / Fedora bluez-libs-devel
  - `wget`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[bluetooth]] · [[bluelog]] · [[BlackArch Tools]]

