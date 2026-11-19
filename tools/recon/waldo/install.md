---
tool: "waldo"
build-system: "scripts"
upstream: "https://github.com/red-team-labs/waldo"
recipe: "blackarch"
source: "https://github.com/red-team-labs/waldo"
license: "MIT"
blackarch-pkgver: "29.ee4f960"
---

# Installing waldo

> A lightweight and multithreaded directory and subdomain bruteforcer implemented in Python.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/waldo) (version `29.ee4f960`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S waldo    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/red-team-labs/waldo) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-requests, python2-simplejson (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[waldo]] · [[BlackArch Tools]]

