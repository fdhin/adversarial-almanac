---
tool: "obexstress"
build-system: "scripts"
upstream: "http://bluetooth-pentest.narod.ru/"
recipe: "blackarch"
source: "http://bluetooth-pentest.narod.ru/software/obexstress.py"
license: "GPL"
blackarch-pkgver: "0.1"
---

# Installing obexstress

> Script for testing remote OBEX service for some potential vulnerabilities.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/obexstress) (version `0.1`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S obexstress    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://bluetooth-pentest.narod.ru/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `lightblue`
  - `python2`
- ⚠️ Python 2 only deps: python2-pybluez (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[bluetooth]] · [[obexstress]] · [[BlackArch Tools]]

