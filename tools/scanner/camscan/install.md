---
tool: "camscan"
build-system: "scripts"
upstream: "https://github.com/securestate/camscan"
recipe: "blackarch"
source: "https://github.com/securestate/camscan"
license: "BSD"
blackarch-pkgver: "1.0057215"
---

# Installing camscan

> A tool which will analyze the CAM table of Cisco switches to look for anamolies.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/camscan) (version `1.0057215`, license BSD).

## Arch / BlackArch

```bash
sudo pacman -S camscan    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/securestate/camscan) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-paramiko, python2-setuptools (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[camscan]] · [[BlackArch Tools]]

