---
tool: "python2-darts.util.lru"
build-system: "scripts"
upstream: "https://pypi.python.org/pypi/darts.util.lru"
recipe: "blackarch"
source: "https://pypi.python.org/pypi/darts.util.lru"
license: "MIT"
blackarch-pkgver: "7.5ef01b1"
---

# Installing python2-darts.util.lru

> Simple dictionary with LRU behaviour.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/python2-darts.util.lru) (version `7.5ef01b1`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S python2-darts.util.lru    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/deterministic-arts/DartsPyLRU.git
cd DartsPyLRU
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-setuptools (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[misc]] · [[python2-darts.util.lru]] · [[BlackArch Tools]]

