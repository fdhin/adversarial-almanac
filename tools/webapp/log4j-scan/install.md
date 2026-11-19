---
tool: "log4j-scan"
build-system: "scripts"
upstream: "https://github.com/fullhunt/log4j-scan"
recipe: "blackarch"
source: "https://github.com/fullhunt/log4j-scan"
license: "MIT"
blackarch-pkgver: "88.07f7e32"
---

# Installing log4j-scan

> A fully automated, accurate, and extensive scanner for finding log4j RCE CVE-44228.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/log4j-scan) (version `88.07f7e32`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S log4j-scan    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/fullhunt/log4j-scan) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `pycryptodome`, `requests`, `termcolor`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[log4j-scan]] · [[BlackArch Tools]]

