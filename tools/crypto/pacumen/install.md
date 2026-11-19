---
tool: "pacumen"
build-system: "scripts"
upstream: "https://github.com/bniemczyk/pacumen"
recipe: "blackarch"
source: "https://github.com/bniemczyk/pacumen"
license: "custom:unknown"
blackarch-pkgver: "1.92a0884"
---

# Installing pacumen

> Packet Acumen - Analyse encrypted network traffic and more (side-channel attacks).

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/pacumen) (version `1.92a0884`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S pacumen    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/bniemczyk/pacumen) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-numpy, python2-pydot, python2-pyqt5, python2-scapy, python2-scipy, python2-setuptools, python2-symath (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[crypto]] · [[pacumen]] · [[BlackArch Tools]]

