---
tool: "buttinsky"
build-system: "scripts"
upstream: "https://github.com/buttinsky/buttinsky"
recipe: "blackarch"
source: "https://github.com/buttinsky/buttinsky"
license: "GPL3"
blackarch-pkgver: "138.1a2a1b2"
---

# Installing buttinsky

> Provide an open source framework for automated botnet monitoring.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/buttinsky) (version `138.1a2a1b2`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S buttinsky    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/buttinsky/buttinsky) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-configobj, python2-gevent, python2-hpfeeds, python2-pysocks, python2-setuptools, python2-validictory (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/buttinsky`

Backlinks: [[networking]] · [[buttinsky]] · [[BlackArch Tools]]

