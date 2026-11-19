---
tool: "maltrail"
build-system: "scripts"
upstream: "https://github.com/stamparm/maltrail"
recipe: "blackarch"
aur-package: "maltrail"
source: "https://github.com/stamparm/maltrail"
license: "MIT"
blackarch-pkgver: "3.5.r111.g1d3bc32"
provides: ["maltrail-sensor", "maltrail-server"]
---

# Installing maltrail

> Malicious traffic detection system.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/maltrail) (version `3.5.r111.g1d3bc32`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S maltrail    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/stamparm/maltrail) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `pcapy`, `pysqlite3`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- Commands: `/usr/bin/maltrail-sensor`, `/usr/bin/maltrail-server`

Backlinks: [[defensive]] · [[maltrail]] · [[BlackArch Tools]]

