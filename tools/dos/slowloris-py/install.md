---
tool: "slowloris-py"
build-system: "scripts"
upstream: "https://github.com/gkbrk/slowloris"
recipe: "blackarch"
source: "https://github.com/gkbrk/slowloris"
license: "BSD"
blackarch-pkgver: "30.eb7f632"
---

# Installing slowloris-py

> Low bandwidth DoS tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/slowloris-py) (version `30.eb7f632`, license BSD).

## Arch / BlackArch

```bash
sudo pacman -S slowloris-py    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/gkbrk/slowloris) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `setuptools`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[dos]] · [[slowloris-py]] · [[BlackArch Tools]]

