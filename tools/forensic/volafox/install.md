---
tool: "volafox"
build-system: "scripts"
upstream: "https://github.com/n0fate/volafox"
recipe: "blackarch"
source: "https://github.com/n0fate/volafox"
license: "GPL2"
blackarch-pkgver: "143.5b42987"
---

# Installing volafox

> Mac OS X Memory Analysis Toolkit.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/volafox) (version `143.5b42987`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S volafox    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/n0fate/volafox) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[forensic]] · [[volafox]] · [[BlackArch Tools]]

