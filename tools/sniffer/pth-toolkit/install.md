---
tool: "pth-toolkit"
build-system: "scripts"
upstream: "https://github.com/byt3bl33d3r/pth-toolkit"
recipe: "blackarch"
source: "https://github.com/byt3bl33d3r/pth-toolkit"
license: "BSD"
blackarch-pkgver: "7.3641cdc"
---

# Installing pth-toolkit

> Modified version of the passing-the-hash tool collection made to work straight out of the box.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/pth-toolkit) (version `7.3641cdc`, license BSD).

## Arch / BlackArch

```bash
sudo pacman -S pth-toolkit    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/byt3bl33d3r/pth-toolkit) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[sniffer]] · [[pth-toolkit]] · [[BlackArch Tools]]

