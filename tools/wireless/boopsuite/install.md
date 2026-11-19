---
tool: "boopsuite"
build-system: "scripts"
upstream: "https://github.com/M1ND-B3ND3R/BoopSuite"
recipe: "blackarch"
source: "https://github.com/M1ND-B3ND3R/BoopSuite"
license: "MIT"
blackarch-pkgver: "1.1c4e68d"
provides: ["boop", "boopsniff", "boopsniff-gui", "boopstrike"]
---

# Installing boopsuite

> A Suite of Tools written in Python for wireless auditing and security testing.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/boopsuite) (version `1.1c4e68d`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S boopsuite    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/M1ND-B3ND3R/BoopSuite.git
cd BoopSuite
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-argparse, python2-netaddr, python2-pyric, python2-scapy, python2-tabulate (legacy; needs python2)

## Installed layout (per recipe)

- Commands: `/usr/bin/boop`, `/usr/bin/boopsniff`, `/usr/bin/boopsniff-gui`, `/usr/bin/boopstrike`

Backlinks: [[wireless]] · [[boopsuite]] · [[BlackArch Tools]]

