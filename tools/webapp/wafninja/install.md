---
tool: "wafninja"
build-system: "scripts"
upstream: "https://github.com/khalilbijjou/WAFNinja"
recipe: "blackarch"
source: "https://github.com/khalilbijjou/WAFNinja"
license: "GPL2"
blackarch-pkgver: "26.ce6ea60"
---

# Installing wafninja

> A tool which contains two functions to attack Web Application Firewalls.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/wafninja) (version `26.ce6ea60`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S wafninja    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/khalilbijjou/WAFNinja.git
cd WAFNinja
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-prettytable, python2-progressbar, python2-pyopenssl, python2-pysqlite (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[wafninja]] · [[BlackArch Tools]]

