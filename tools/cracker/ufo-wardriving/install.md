---
tool: "ufo-wardriving"
build-system: "scripts"
upstream: "http://www.ufo-wardriving.com/"
recipe: "blackarch"
source: "http://www.ufo-wardriving.com/"
license: "GPL2"
blackarch-pkgver: "4"
---

# Installing ufo-wardriving

> Allows you to test the security of wireless networks by detecting their passwords based on the router model.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/ufo-wardriving) (version `4`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S ufo-wardriving    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://www.ufo-wardriving.com/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `python2`
  - `qtwebkit`
- ⚠️ Python 2 only deps: python2-pyqt4 (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[cracker]] · [[ufo-wardriving]] · [[BlackArch Tools]]

