---
tool: "sqlivulscan"
build-system: "scripts"
upstream: "https://github.com/Hadesy2k/sqlivulscan"
recipe: "blackarch"
source: "https://github.com/Hadesy2k/sqlivulscan"
license: "GPL2"
blackarch-pkgver: "249.cc8e657"
---

# Installing sqlivulscan

> This will give you the SQLi Vulnerable Website Just by Adding the Dork.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/sqlivulscan) (version `249.cc8e657`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S sqlivulscan    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/Hadesy2k/sqlivulscan) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-beautifulsoup4, python2-nyawc, python2-setuptools, python2-simplejson, python2-termcolor, python2-terminaltables (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[sqlivulscan]] · [[BlackArch Tools]]

