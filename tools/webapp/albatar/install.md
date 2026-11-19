---
tool: "albatar"
build-system: "scripts"
upstream: "https://github.com/lanjelot/albatar"
recipe: "blackarch"
source: "https://github.com/lanjelot/albatar"
license: "custom:unknown"
blackarch-pkgver: "0.1.r11.gd500657"
---

# Installing albatar

> A SQLi exploitation framework in Python.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/albatar) (version `0.1.r11.gd500657`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S albatar    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/lanjelot/albatar) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-pycurl (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[albatar]] · [[BlackArch Tools]]

