---
tool: "blindy"
build-system: "scripts"
upstream: "https://github.com/missDronio/blindy"
recipe: "blackarch"
source: "https://github.com/missDronio/blindy"
license: "custom:unknown"
blackarch-pkgver: "12.59de8f2"
---

# Installing blindy

> Simple script to automate brutforcing blind sql injection vulnerabilities.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/blindy) (version `12.59de8f2`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S blindy    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/missDronio/blindy) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `requests`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[blindy]] · [[BlackArch Tools]]

