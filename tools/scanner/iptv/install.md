---
tool: "iptv"
build-system: "scripts"
upstream: "https://github.com/Pinperepette/IPTV"
recipe: "blackarch"
source: "https://github.com/Pinperepette/IPTV"
license: "custom:unknown"
blackarch-pkgver: "138.ae6457b"
provides: ["iptv-cli", "iptv-gui"]
---

# Installing iptv

> Search and brute force illegal iptv server.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/iptv) (version `138.ae6457b`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S iptv    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/Pinperepette/IPTV.git
cd IPTV
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-clint, python2-colored, python2-google, python2-psutil, python2-pyprind, python2-pyqt4 (legacy; needs python2)

## Installed layout (per recipe)

- Commands: `/usr/bin/iptv-cli`, `/usr/bin/iptv-gui`

Backlinks: [[scanner]] · [[iptv]] · [[BlackArch Tools]]

