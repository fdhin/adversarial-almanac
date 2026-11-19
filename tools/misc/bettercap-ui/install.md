---
tool: "bettercap-ui"
build-system: "scripts"
upstream: "https://github.com/bettercap/ui"
recipe: "blackarch"
aur-package: "bettercap-ui"
source: "https://github.com/bettercap/ui"
license: "GPL-3.0-or-later"
blackarch-pkgver: "1.4.0"
---

# Installing bettercap-ui

> Official Bettercap's Web UI.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/bettercap-ui) (version `1.4.0`, license GPL-3.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S bettercap-ui    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/bettercap/ui) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/bettercap`

Backlinks: [[misc]] · [[bettercap-ui]] · [[BlackArch Tools]]

