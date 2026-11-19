---
tool: "gatecrasher"
build-system: "scripts"
upstream: "https://github.com/michaeltelford/gatecrasher"
recipe: "blackarch"
source: "https://github.com/michaeltelford/gatecrasher"
license: "BSD"
blackarch-pkgver: "2.3ad5225"
---

# Installing gatecrasher

> Network auditing and analysis tool developed in Python.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/gatecrasher) (version `2.3ad5225`, license BSD).

## Arch / BlackArch

```bash
sudo pacman -S gatecrasher    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/michaeltelford/gatecrasher) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[gatecrasher]] · [[BlackArch Tools]]

