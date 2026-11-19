---
tool: "jaadas"
build-system: "scripts"
upstream: "https://github.com/flankerhqd/JAADAS/"
recipe: "blackarch"
source: "https://github.com/flankerhqd/JAADAS/"
license: "GPL3"
blackarch-pkgver: "0.1"
---

# Installing jaadas

> Joint Advanced Defect assEsment for android applications.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/jaadas) (version `0.1`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S jaadas    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/flankerhqd/JAADAS/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`, `java-environment`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[jaadas]] · [[BlackArch Tools]]

