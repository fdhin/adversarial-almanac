---
tool: "openvas"
build-system: "scripts"
upstream: "https://www.blackarch.org/"
recipe: "blackarch"
source: "https://www.blackarch.org/"
license: "custom:unknown"
blackarch-pkgver: "20250606"
---

# Installing openvas

> Meta package for installing all OpenVAS components.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/openvas) (version `20250606`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S openvas    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://www.blackarch.org/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[openvas]] · [[BlackArch Tools]]

