---
tool: "bleah"
build-system: "scripts"
upstream: "https://github.com/evilsocket/bleah"
recipe: "blackarch"
source: "https://github.com/evilsocket/bleah"
license: "GPL3"
blackarch-pkgver: "54.56672e8"
---

# Installing bleah

> A BLE scanner for "smart" devices hacking.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/bleah) (version `54.56672e8`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S bleah    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/evilsocket/bleah) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-argparse, python2-bluepy, python2-terminaltables (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[bleah]] · [[BlackArch Tools]]

