---
tool: "taof"
build-system: "scripts"
upstream: "http://taof.sf.net/"
recipe: "blackarch"
source: "http://taof.sf.net/"
license: "GPL"
blackarch-pkgver: "0.3.2"
---

# Installing taof

> A GUI cross-platform Python generic network protocol fuzzer.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/taof) (version `0.3.2`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S taof    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://taof.sf.net/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `pygtk`
  - `python2`
- ⚠️ Python 2 only deps: python2-gobject, python2-twistedx (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[fuzzer]] · [[taof]] · [[BlackArch Tools]]

