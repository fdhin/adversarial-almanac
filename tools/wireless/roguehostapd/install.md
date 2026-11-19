---
tool: "roguehostapd"
build-system: "other"
upstream: "https://github.com/wifiphisher/roguehostapd"
recipe: "blackarch"
source: "https://github.com/wifiphisher/roguehostapd"
license: "BSD-3-Clause"
blackarch-pkgver: "1.0.r45.g381b373"
---

# Installing roguehostapd

> Hostapd fork including Wi-Fi attacks and providing Python bindings with ctypes.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/roguehostapd) (version `1.0.r45.g381b373`, license BSD-3-Clause).

## Arch / BlackArch

```bash
sudo pacman -S roguehostapd    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/wifiphisher/roguehostapd) (the recipe uses a versioned archive).

```bash
# see exact recipe steps below
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `libnfnetlink`
  - `libnl`
  - `python2`
- ⚠️ Python 2 only deps: python2-configparser, python2-setuptools (legacy; needs python2)

## Exact build steps from the recipe

```bash
cd $pkgname

  python2 setup.py build
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[wireless]] · [[roguehostapd]] · [[BlackArch Tools]]

