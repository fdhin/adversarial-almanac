---
tool: "wpforce"
build-system: "scripts"
upstream: "https://github.com/n00py/WPForce"
recipe: "blackarch"
source: "https://github.com/n00py/WPForce"
license: "custom:unknown"
blackarch-pkgver: "88.b72ec64"
provides: ["wpforce"]
---

# Installing wpforce

> Wordpress Attack Suite.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/wpforce) (version `88.b72ec64`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S wpforce    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/n00py/WPForce.git
cd WPForce
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-argparse, python2-requests (legacy; needs python2)

## Installed layout (per recipe)

- Commands: `/usr/bin/wpforce`

Backlinks: [[webapp]] · [[wpforce]] · [[BlackArch Tools]]

