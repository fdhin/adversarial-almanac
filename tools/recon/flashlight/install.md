---
tool: "flashlight"
build-system: "scripts"
upstream: "https://github.com/galkan/flashlight"
recipe: "blackarch"
source: "https://github.com/galkan/flashlight"
license: "custom:unknown"
blackarch-pkgver: "109.90d1dc5"
---

# Installing flashlight

> Automated Information Gathering Tool for Penetration Testers.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/flashlight) (version `109.90d1dc5`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S flashlight    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/galkan/flashlight) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-yaml (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[flashlight]] · [[BlackArch Tools]]

