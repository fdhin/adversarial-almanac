---
tool: "pintool2"
build-system: "scripts"
upstream: "https://github.com/sebastiendamaye/pintool2"
recipe: "blackarch"
source: "https://github.com/sebastiendamaye/pintool2"
license: "custom:unknown"
blackarch-pkgver: "5.1c1af91"
---

# Installing pintool2

> Improved version of pintool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/pintool2) (version `5.1c1af91`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S pintool2    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/sebastiendamaye/pintool2) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `pin`
  - `python2`
- ⚠️ Python 2 only deps: python2-argparse (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[reversing]] · [[pintool2]] · [[BlackArch Tools]]

