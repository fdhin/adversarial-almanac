---
tool: "exitmap"
build-system: "scripts"
upstream: "https://github.com/NullHypothesis/exitmap"
recipe: "blackarch"
source: "https://github.com/NullHypothesis/exitmap"
license: "GPL3"
blackarch-pkgver: "373.8155029"
---

# Installing exitmap

> A fast and modular scanner for Tor exit relays.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/exitmap) (version `373.8155029`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S exitmap    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/NullHypothesis/exitmap) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
  - `tor`
- ⚠️ Python 2 only deps: python2-configparser, python2-dnspython, python2-pysocks, python2-stem (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[exitmap]] · [[BlackArch Tools]]

