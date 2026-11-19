---
tool: "azazel"
build-system: "scripts"
upstream: "https://github.com/chokepoint/azazel"
recipe: "blackarch"
source: "https://github.com/chokepoint/azazel"
license: "GPL2"
blackarch-pkgver: "15.a41fbb5"
---

# Installing azazel

> A userland rootkit based off of the original LD_PRELOAD technique from Jynx rootkit.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/azazel) (version `15.a41fbb5`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S azazel    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/chokepoint/azazel) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[backdoor]] · [[azazel]] · [[BlackArch Tools]]

