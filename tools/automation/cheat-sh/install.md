---
tool: "cheat-sh"
build-system: "scripts"
upstream: "https://cheat.sh"
recipe: "blackarch"
source: "https://cheat.sh"
license: "MIT"
blackarch-pkgver: "6"
---

# Installing cheat-sh

> The only cheat sheet you need.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/cheat-sh) (version `6`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S cheat-sh    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://cheat.sh) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `virtualenv`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/bash-completion`

Backlinks: [[automation]] · [[cheat-sh]] · [[BlackArch Tools]]

