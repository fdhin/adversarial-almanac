---
tool: "wifiscanmap"
build-system: "scripts"
upstream: "https://github.com/mehdilauters/wifiScanMap"
recipe: "blackarch"
source: "https://github.com/mehdilauters/wifiScanMap"
license: "GPL3"
blackarch-pkgver: "135.9adcd08"
---

# Installing wifiscanmap

> Another wifi mapping tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/wifiscanmap) (version `135.9adcd08`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S wifiscanmap    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/mehdilauters/wifiScanMap.git
cd wifiScanMap
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `aircrack-ng`
  - `bluez`
  - `bower`
  - `gpsd`
  - `python2`
- ⚠️ Python 2 only deps: python2-gps, python2-pysqlite, python2-rpi-gpio, python2-scapy (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[wireless]] · [[wifiscanmap]] · [[BlackArch Tools]]

