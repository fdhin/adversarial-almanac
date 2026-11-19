---
tool: "secscan"
build-system: "scripts"
upstream: "https://code.google.com/archive/p/secscan-py/"
recipe: "blackarch"
source: "https://code.google.com/archive/p/secscan-py/"
license: "GPL-3.0-or-later"
blackarch-pkgver: "1.5"
---

# Installing secscan

> Web Apps Scanner and Much more utilities.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/secscan) (version `1.5`, license GPL-3.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S secscan    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://code.google.com/archive/p/secscan-py/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `python2`
  - `unzip`
- ⚠️ Python 2 only deps: python2-paramiko (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[secscan]] · [[BlackArch Tools]]

