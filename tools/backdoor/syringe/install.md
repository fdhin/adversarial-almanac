---
tool: "syringe"
build-system: "scripts"
upstream: "https://github.com/securestate/syringe"
recipe: "blackarch"
source: "https://github.com/securestate/syringe"
license: "GPL3"
blackarch-pkgver: "12.79a703e"
---

# Installing syringe

> A General Purpose DLL & Code Injection Utility.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/syringe) (version `12.79a703e`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S syringe    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/securestate/syringe) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[backdoor]] · [[syringe]] · [[BlackArch Tools]]

