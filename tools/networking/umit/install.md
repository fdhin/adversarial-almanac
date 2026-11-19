---
tool: "umit"
build-system: "scripts"
upstream: "http://www.umitproject.org/"
recipe: "blackarch"
source: "http://www.umitproject.org/"
license: "GPL"
blackarch-pkgver: "1.0"
---

# Installing umit

> A powerful nmap frontend.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/umit) (version `1.0`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S umit    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://www.umitproject.org/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `nmap`
  - `pygtk`
  - `python2`
- ⚠️ Python 2 only deps: python2-pysqlite (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/applications`

Backlinks: [[networking]] · [[umit]] · [[BlackArch Tools]]

