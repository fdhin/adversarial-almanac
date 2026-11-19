---
tool: "ilty"
build-system: "scripts"
upstream: "http://chdir.org/~nico/ilty/"
recipe: "blackarch"
source: "http://chdir.org/~nico/ilty/"
license: "GPL2"
blackarch-pkgver: "1.0"
---

# Installing ilty

> An interception phone system for VoIP network.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/ilty) (version `1.0`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S ilty    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://chdir.org/~nico/ilty/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `python2`
  - `sox`
- ⚠️ Python 2 only deps: python2-pylibpcap, python2-pypcap (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[voip]] · [[ilty]] · [[BlackArch Tools]]

