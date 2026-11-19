---
tool: "anontwi"
build-system: "scripts"
upstream: "http://anontwi.sourceforge.net/"
recipe: "blackarch"
source: "http://anontwi.sourceforge.net/"
license: "custom:unknown"
blackarch-pkgver: "1.1b"
---

# Installing anontwi

> A free software python client designed to navigate anonymously on social networks. It supports Identi.ca and Twitter.com.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/anontwi) (version `1.1b`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S anontwi    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://anontwi.sourceforge.net/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `gtk2`
  - `pygtk`
  - `python2`
- ⚠️ Python 2 only deps: python2-httplib2, python2-pycryptodome, python2-pycurl (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[social]] · [[anontwi]] · [[BlackArch Tools]]

