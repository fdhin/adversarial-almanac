---
tool: "smalisca"
build-system: "scripts"
upstream: "https://github.com/dorneanu/smalisca"
recipe: "blackarch"
source: "https://github.com/dorneanu/smalisca"
license: "MIT"
blackarch-pkgver: "58.1aa7a16"
---

# Installing smalisca

> Static Code Analysis for Smali files.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/smalisca) (version `58.1aa7a16`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S smalisca    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/dorneanu/smalisca) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `graphviz`
  - `python2`
- ⚠️ Python 2 only deps: python2-cement, python2-configparser, python2-flask, python2-flask-restless, python2-flask-sqlalchemy, python2-mimerender, python2-prettytable, python2-pyfiglet, python2-pygraphviz, python2-setuptools, python2-sqlalchemy, python2-werkzeug (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[mobile]] · [[smalisca]] · [[BlackArch Tools]]

