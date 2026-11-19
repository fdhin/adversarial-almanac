---
tool: "rawr"
build-system: "scripts"
upstream: "https://bitbucket.org/al14s/rawr/wiki/Home"
recipe: "blackarch"
aur-package: "rawr"
source: "https://bitbucket.org/al14s/rawr/wiki/Home"
license: "GPL-3.0-or-later"
blackarch-pkgver: "74.544dd75"
---

# Installing rawr

> Rapid Assessment of Web Resources. A web enumerator.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/rawr) (version `74.544dd75`, license GPL-3.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S rawr    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://bitbucket.org/al14s/rawr/wiki/Home) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `graphviz`
  - `nmap`
  - `phantomjs`
  - `python2`
- ⚠️ Python 2 only deps: python2-imaging, python2-lxml, python2-pillow, python2-pygraphviz, python2-pyqt4, python2-rdpy, python2-xlsxwriter (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[rawr]] · [[BlackArch Tools]]

