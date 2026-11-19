---
tool: "sdn-toolkit"
build-system: "scripts"
upstream: "http://downloads.sourceforge.net/project/sdn-toolkit"
recipe: "blackarch"
source: "http://downloads.sourceforge.net/project/sdn-toolkit"
license: "GPL3"
blackarch-pkgver: "1.21"
---

# Installing sdn-toolkit

> Discover, Identify, and Manipulate SDN-Based Networks

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/sdn-toolkit) (version `1.21`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S sdn-toolkit    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://downloads.sourceforge.net/project/sdn-toolkit) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `dos2unix`
  - `python2`
- ⚠️ Python 2 only deps: python2-dpkt, python2-simplejson (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[networking]] · [[sdn-toolkit]] · [[BlackArch Tools]]

