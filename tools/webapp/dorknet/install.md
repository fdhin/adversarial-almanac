---
tool: "dorknet"
build-system: "scripts"
upstream: "https://github.com/NullArray/DorkNet"
recipe: "blackarch"
source: "https://github.com/NullArray/DorkNet"
license: "MIT"
blackarch-pkgver: "58.419d6a2"
---

# Installing dorknet

> Selenium powered Python script to automate searching for vulnerable web apps.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/dorknet) (version `58.419d6a2`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S dorknet    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/NullArray/DorkNet.git
cd DorkNet
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-argparse, python2-blessings, python2-selenium, python2-six (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[dorknet]] · [[BlackArch Tools]]

