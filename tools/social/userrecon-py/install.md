---
tool: "userrecon-py"
build-system: "scripts"
upstream: "https://github.com/lucmski/userrecon-py"
recipe: "blackarch"
source: "https://github.com/lucmski/userrecon-py"
license: "MIT"
blackarch-pkgver: "15.eebd422"
---

# Installing userrecon-py

> Recognition usernames in 187 social networks.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/userrecon-py) (version `15.eebd422`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S userrecon-py    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/lucmski/userrecon-py) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `aiohttp`, `colorama`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[social]] · [[userrecon-py]] · [[BlackArch Tools]]

