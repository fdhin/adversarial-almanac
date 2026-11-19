---
tool: "patchkit"
build-system: "scripts"
upstream: "https://github.com/lunixbochs/patchkit"
recipe: "blackarch"
source: "https://github.com/lunixbochs/patchkit"
license: "BSD, custom:unknown"
blackarch-pkgver: "37.95dc699"
---

# Installing patchkit

> Powerful binary patching from Python.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/patchkit) (version `37.95dc699`, license BSD, custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S patchkit    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/lunixbochs/patchkit) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
  - `unicorn`
- ⚠️ Python 2 only deps: python2-capstone, python2-coding, python2-keystone (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[binary]] · [[patchkit]] · [[BlackArch Tools]]

