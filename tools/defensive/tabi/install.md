---
tool: "tabi"
build-system: "scripts"
upstream: "https://github.com/ANSSI-FR/tabi"
recipe: "blackarch"
source: "https://github.com/ANSSI-FR/tabi"
license: "MIT"
blackarch-pkgver: "13.068a406"
---

# Installing tabi

> BGP Hijack Detection.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/tabi) (version `13.068a406`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S tabi    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/ANSSI-FR/tabi) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-dateutil, python2-py-radix, python2-setuptools (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[defensive]] · [[tabi]] · [[BlackArch Tools]]

