---
tool: "nosqlmap"
build-system: "scripts"
upstream: "https://github.com/tcstool/NoSQLMap"
recipe: "blackarch"
source: "https://github.com/tcstool/NoSQLMap"
license: "GPL3"
blackarch-pkgver: "304.5a6dc7a"
---

# Installing nosqlmap

> Automated Mongo database and NoSQL web application exploitation tool

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/nosqlmap) (version `304.5a6dc7a`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S nosqlmap    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/tcstool/NoSQLMap.git
cd NoSQLMap
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `mongodb`
  - `python2`
- ⚠️ Python 2 only deps: python2-couchdb, python2-httplib2, python2-ipcalc, python2-pbkdf2, python2-pymongo, python2-requests, python2-setuptools, python2-six (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[nosqlmap]] · [[BlackArch Tools]]

