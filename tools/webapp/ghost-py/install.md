---
tool: "ghost-py"
build-system: "scripts"
upstream: "http://jeanphix.github.com/Ghost.py/"
recipe: "blackarch"
source: "http://jeanphix.github.com/Ghost.py/"
license: "MIT"
blackarch-pkgver: "2.0.0"
---

# Installing ghost-py

> Webkit based webclient (relies on PyQT).

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/ghost-py) (version `2.0.0`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S ghost-py    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://jeanphix.github.com/Ghost.py/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-distribute, python2-pyqt (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/ghost`

Backlinks: [[webapp]] · [[ghost-py]] · [[BlackArch Tools]]

