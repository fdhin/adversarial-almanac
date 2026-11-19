---
tool: "ibrute"
build-system: "scripts"
upstream: "https://github.com/hackappcom/ibrute/"
recipe: "blackarch"
source: "https://github.com/hackappcom/ibrute/"
license: "custom:unknown"
blackarch-pkgver: "12.3a6a11e"
---

# Installing ibrute

> An AppleID password bruteforce tool. It uses Find My Iphone service API, where bruteforce protection was not implemented.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/ibrute) (version `12.3a6a11e`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S ibrute    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/hackappcom/ibrute/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-lxml, python2-simplejson (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[cracker]] · [[ibrute]] · [[BlackArch Tools]]

