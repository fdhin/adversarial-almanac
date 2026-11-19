---
tool: "cangibrina"
build-system: "scripts"
upstream: "https://github.com/fnk0c/cangibrina"
recipe: "blackarch"
source: "https://github.com/fnk0c/cangibrina"
license: "GPL2"
blackarch-pkgver: "123.6de0165"
---

# Installing cangibrina

> Dashboard Finder.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/cangibrina) (version `123.6de0165`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S cangibrina    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/fnk0c/cangibrina) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-beautifulsoup4, python2-html5lib, python2-mechanize, python2-pysocks (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[cangibrina]] · [[BlackArch Tools]]

