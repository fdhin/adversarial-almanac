---
tool: "evtkit"
build-system: "scripts"
upstream: "https://github.com/yarox24/evtkit"
recipe: "blackarch"
source: "https://github.com/yarox24/evtkit"
license: "MIT"
blackarch-pkgver: "8.af06db3"
---

# Installing evtkit

> Fix acquired .evt - Windows Event Log files (Forensics).

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/evtkit) (version `8.af06db3`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S evtkit    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/yarox24/evtkit) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[forensic]] · [[evtkit]] · [[BlackArch Tools]]

