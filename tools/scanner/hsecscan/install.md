---
tool: "hsecscan"
build-system: "scripts"
upstream: "https://github.com/riramar/hsecscan"
recipe: "blackarch"
source: "https://github.com/riramar/hsecscan"
license: "GPL2"
blackarch-pkgver: "67.aa5845b"
---

# Installing hsecscan

> A security scanner for HTTP response headers.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/hsecscan) (version `67.aa5845b`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S hsecscan    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/riramar/hsecscan) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-pysqlite, python2-setuptools, python2-simplejson (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[hsecscan]] · [[BlackArch Tools]]

