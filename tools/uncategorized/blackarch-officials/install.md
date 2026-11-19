---
tool: "blackarch-officials"
build-system: "scripts"
upstream: "https://www.blackarch.org/"
recipe: "blackarch"
source: "https://www.blackarch.org/"
license: "custom:unknown"
blackarch-pkgver: "20260910"
---

# Installing blackarch-officials

> Meta package for installing official security tools from the Arch Linux repository.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/blackarch-officials) (version `20260910`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S blackarch-officials    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://www.blackarch.org/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[uncategorized]] · [[blackarch-officials]] · [[BlackArch Tools]]

