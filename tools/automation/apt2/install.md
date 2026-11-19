---
tool: "apt2"
build-system: "scripts"
upstream: "https://github.com/MooseDojo/apt2"
recipe: "blackarch"
source: "https://github.com/MooseDojo/apt2"
license: "custom:unknown"
blackarch-pkgver: "v1.0.20180315.r12.g8075cdc"
---

# Installing apt2

> Automated penetration toolkit.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/apt2) (version `v1.0.20180315.r12.g8075cdc`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S apt2    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/MooseDojo/apt2) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-argparse, python2-msgpack, python2-scapy, python2-setuptools, python2-unqlite, python2-yattag (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[automation]] · [[apt2]] · [[BlackArch Tools]]

