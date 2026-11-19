---
tool: "wifitap"
build-system: "scripts"
upstream: "https://github.com/GDSSecurity/wifitap"
recipe: "blackarch"
source: "https://github.com/GDSSecurity/wifitap"
license: "GPL2"
blackarch-pkgver: "2b16088"
---

# Installing wifitap

> WiFi injection tool through tun/tap device.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/wifitap) (version `2b16088`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S wifitap    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/GDSSecurity/wifitap) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-scapy (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[wireless]] · [[wifitap]] · [[BlackArch Tools]]

