---
tool: "flowinspect"
build-system: "scripts"
upstream: "https://github.com/7h3rAm/flowinspect"
recipe: "blackarch"
source: "https://github.com/7h3rAm/flowinspect"
license: "custom"
blackarch-pkgver: "97.34759ed"
---

# Installing flowinspect

> A network traffic inspection tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/flowinspect) (version `97.34759ed`, license custom).

## Arch / BlackArch

```bash
sudo pacman -S flowinspect    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/7h3rAm/flowinspect) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-fuzzywuzzy, python2-pylibemu, python2-pynids, python2-re2, python2-yara (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[networking]] · [[flowinspect]] · [[BlackArch Tools]]

