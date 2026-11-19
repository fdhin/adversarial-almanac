---
tool: "webkiller"
build-system: "scripts"
upstream: "https://github.com/ultrasecurity/webkiller"
recipe: "blackarch"
source: "https://github.com/ultrasecurity/webkiller"
license: "custom:unknown"
blackarch-pkgver: "36.7ad72d3"
---

# Installing webkiller

> Tool Information Gathering Write By Python.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/webkiller) (version `36.7ad72d3`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S webkiller    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/ultrasecurity/webkiller) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `builtwith`, `colorama`, `ipapi`, `requests`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[webkiller]] · [[BlackArch Tools]]

