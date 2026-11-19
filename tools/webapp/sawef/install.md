---
tool: "sawef"
build-system: "scripts"
upstream: "https://github.com/danilovazb/sawef"
recipe: "blackarch"
source: "https://github.com/danilovazb/sawef"
license: "GPL3"
blackarch-pkgver: "32.e5ce862"
---

# Installing sawef

> Send Attack Web Forms.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/sawef) (version `32.e5ce862`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S sawef    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/danilovazb/sawef) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-beautifulsoup4, python2-requests, python2-simplejson (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[sawef]] · [[BlackArch Tools]]

