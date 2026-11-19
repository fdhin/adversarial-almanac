---
tool: "bbqsql"
build-system: "scripts"
upstream: "https://github.com/neohapsis/bbqsql"
recipe: "blackarch"
source: "https://github.com/neohapsis/bbqsql"
license: "BSD"
blackarch-pkgver: "261.b9859d2"
---

# Installing bbqsql

> SQL injection exploit tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/bbqsql) (version `261.b9859d2`, license BSD).

## Arch / BlackArch

```bash
sudo pacman -S bbqsql    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/neohapsis/bbqsql) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-configparser, python2-eventlet, python2-gevent, python2-requests, python2-setuptools (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[bbqsql]] · [[BlackArch Tools]]

