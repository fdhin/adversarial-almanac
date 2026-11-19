---
tool: "doork"
build-system: "scripts"
upstream: "https://github.com/AeonDave/doork"
recipe: "blackarch"
source: "https://github.com/AeonDave/doork"
license: "MIT"
blackarch-pkgver: "6.90c7260"
---

# Installing doork

> Passive Vulnerability Auditor.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/doork) (version `6.90c7260`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S doork    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/AeonDave/doork) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-beautifulsoup4, python2-django, python2-requests, python2-simplejson, python2-xgoogle (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[doork]] · [[BlackArch Tools]]

