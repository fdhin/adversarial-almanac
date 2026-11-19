---
tool: "lightbulb"
build-system: "scripts"
upstream: "https://github.com/lightbulb-framework/lightbulb-framework"
recipe: "blackarch"
source: "https://github.com/lightbulb-framework/lightbulb-framework"
license: "MIT"
blackarch-pkgver: "88.9e8d6f3"
---

# Installing lightbulb

> Python framework for auditing web applications firewalls.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/lightbulb) (version `88.9e8d6f3`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S lightbulb    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/lightbulb-framework/lightbulb-framework.git
cd lightbulb-framework
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-backports.functools_lru_cache, python2-cliff, python2-contextlib2, python2-prettytable, python2-setuptools, python2-sfalearn, python2-symautomata (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[lightbulb]] · [[BlackArch Tools]]

