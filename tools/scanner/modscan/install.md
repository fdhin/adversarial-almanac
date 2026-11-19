---
tool: "modscan"
build-system: "scripts"
upstream: "https://code.google.com/p/modscan/"
recipe: "blackarch"
source: "https://code.google.com/p/modscan/"
license: "GPL3"
blackarch-pkgver: "0.1"
---

# Installing modscan

> A new tool designed to map a SCADA MODBUS TCP based network.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/modscan) (version `0.1`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S modscan    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://code.google.com/p/modscan/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-ipy (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[modscan]] · [[BlackArch Tools]]

