---
tool: "yasat"
build-system: "scripts"
upstream: "https://yasat.sourceforge.net/"
recipe: "blackarch"
aur-package: "yasat"
source: "https://yasat.sourceforge.net/"
license: "GPL3"
blackarch-pkgver: "848"
---

# Installing yasat

> Yet Another Stupid Audit Tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/yasat) (version `848`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S yasat    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://yasat.sourceforge.net/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/man`

Backlinks: [[scanner]] · [[yasat]] · [[BlackArch Tools]]

