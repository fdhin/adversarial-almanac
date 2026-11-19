---
tool: "rfidiot"
build-system: "scripts"
upstream: "http://rfidiot.org/"
recipe: "blackarch"
source: "http://rfidiot.org/"
license: "GPL"
blackarch-pkgver: "331.57c030b"
---

# Installing rfidiot

> An open source python library for exploring RFID devices.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/rfidiot) (version `331.57c030b`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S rfidiot    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/AdamLaurie/RFIDIOt.git
cd RFIDIOt
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `openssl` — Debian libssl-dev / Fedora openssl-devel
  - `python2`
- ⚠️ Python 2 only deps: python2-distribute, python2-pycryptodome (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[wireless]] · [[rfidiot]] · [[BlackArch Tools]]

