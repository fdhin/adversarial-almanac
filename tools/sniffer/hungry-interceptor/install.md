---
tool: "hungry-interceptor"
build-system: "scripts"
upstream: "https://github.com/nbuechler/hungry-interceptor"
recipe: "blackarch"
source: "https://github.com/nbuechler/hungry-interceptor"
license: "GPL3"
blackarch-pkgver: "391.1aea7f3"
---

# Installing hungry-interceptor

> Intercepts data, does something with it, stores it.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/hungry-interceptor) (version `391.1aea7f3`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S hungry-interceptor    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/nbuechler/hungry-interceptor) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-flask-cors, python2-flask-pymongo, python2-flask-wtf, python2-py2neo, python2-pymongo (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[sniffer]] · [[hungry-interceptor]] · [[BlackArch Tools]]

