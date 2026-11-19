---
tool: "birp"
build-system: "scripts"
upstream: "https://github.com/sensepost/birp"
recipe: "blackarch"
source: "https://github.com/sensepost/birp"
license: "custom:unknown"
blackarch-pkgver: "65.b2e108a"
---

# Installing birp

> A tool that will assist in the security assessment of mainframe applications served over TN3270.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/birp) (version `65.b2e108a`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S birp    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/sensepost/birp) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `fontconfig`
  - `ipython2`
  - `python2`
  - `x3270`
  - `xorg-mkfontdir`
  - `xorg-mkfontscale`
  - `xorg-xset`
- ⚠️ Python 2 only deps: python2-backports.functools_lru_cache, python2-colorama, python2-ipython-genutils, python2-py3270 (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[birp]] · [[BlackArch Tools]]

