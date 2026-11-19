---
tool: "hackersh"
build-system: "scripts"
upstream: "http://www.hackersh.org/"
recipe: "blackarch"
source: "http://www.hackersh.org/"
license: "GPL"
blackarch-pkgver: "0.2.0"
---

# Installing hackersh

> A shell for with Pythonect-like syntax, including wrappers for commonly used security tools.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/hackersh) (version `0.2.0`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S hackersh    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://www.hackersh.org/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `python2`
  - `pythonect`
- ⚠️ Python 2 only deps: python2-netaddr, python2-networkx, python2-prettytable, python2-setuptools (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[automation]] · [[hackersh]] · [[BlackArch Tools]]

