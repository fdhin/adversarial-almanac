---
tool: "sprayingtoolkit"
build-system: "scripts"
upstream: "https://github.com/byt3bl33d3r/$_pkgname"
recipe: "blackarch"
source: "https://github.com/byt3bl33d3r/$_pkgname"
license: "GPL-3.0-only"
blackarch-pkgver: "60.82e2ec8"
---

# Installing sprayingtoolkit

> Scripts to make password spraying attacks against Lync/S4B & OWA a lot quicker, less painful and more efficient.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/sprayingtoolkit) (version `60.82e2ec8`, license GPL-3.0-only).

## Arch / BlackArch

```bash
sudo pacman -S sprayingtoolkit    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/byt3bl33d3r/SprayingToolkit) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `virtualenv`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[sprayingtoolkit]] · [[BlackArch Tools]]

