---
tool: "fireprox"
build-system: "scripts"
upstream: "https://github.com/ustayready/fireprox"
recipe: "blackarch"
source: "https://github.com/ustayready/fireprox"
license: "GPL-3.0-only"
blackarch-pkgver: "51.a6e1300"
---

# Installing fireprox

> AWS API Gateway management tool for creating on the fly HTTP pass-through proxies for unique IP rotation.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/fireprox) (version `51.a6e1300`, license GPL-3.0-only).

## Arch / BlackArch

```bash
sudo pacman -S fireprox    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/ustayready/fireprox) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `pip`, `virtualenv`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[proxy]] · [[fireprox]] · [[BlackArch Tools]]

