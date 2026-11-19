---
tool: "dynamorio"
build-system: "scripts"
upstream: "https://github.com/DynamoRIO/dynamorio"
recipe: "blackarch"
aur-package: "dynamorio"
source: "https://github.com/DynamoRIO/dynamorio"
license: "BSD"
blackarch-pkgver: "9.0.19046"
---

# Installing dynamorio

> A dynamic binary instrumentation framework.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/dynamorio) (version `9.0.19046`, license BSD).

## Arch / BlackArch

```bash
sudo pacman -S dynamorio    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/DynamoRIO/dynamorio) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[binary]] · [[dynamorio]] · [[BlackArch Tools]]

