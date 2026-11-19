---
tool: "multiscanner"
build-system: "scripts"
upstream: "https://github.com/mitre/multiscanner"
recipe: "blackarch"
source: "https://github.com/mitre/multiscanner"
license: "Mozilla"
blackarch-pkgver: "1559.86e0145"
---

# Installing multiscanner

> Modular file scanning/analysis framework.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/multiscanner) (version `1559.86e0145`, license Mozilla).

## Arch / BlackArch

```bash
sudo pacman -S multiscanner    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/mitre/multiscanner) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-bitstring, python2-configparser, python2-elasticsearch, python2-flask, python2-magic, python2-paramiko, python2-pefile, python2-pyclamd, python2-pymongo, python2-requests, python2-sqlalchemy, python2-sqlalchemy-utils, python2-ssdeep (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[multiscanner]] · [[BlackArch Tools]]

