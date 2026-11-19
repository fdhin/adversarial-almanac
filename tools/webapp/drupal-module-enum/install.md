---
tool: "drupal-module-enum"
build-system: "scripts"
upstream: "https://github.com/Tethik/drupal-module-enumeration"
recipe: "blackarch"
source: "https://github.com/Tethik/drupal-module-enumeration"
license: "MIT"
blackarch-pkgver: "11.525543c"
---

# Installing drupal-module-enum

> Enumerate on drupal modules.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/drupal-module-enum) (version `11.525543c`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S drupal-module-enum    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/Tethik/drupal-module-enumeration.git
cd drupal-module-enumeration
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-httplib2 (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[drupal-module-enum]] · [[BlackArch Tools]]

