---
tool: "favfreak"
build-system: "scripts"
upstream: "https://github.com/devanshbatham/FavFreak"
recipe: "blackarch"
source: "https://github.com/devanshbatham/FavFreak"
license: "MIT"
blackarch-pkgver: "27.8acea5e"
---

# Installing favfreak

> Weaponizing favicon.ico for BugBounties , OSINT and what not.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/favfreak) (version `27.8acea5e`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S favfreak    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/devanshbatham/FavFreak) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `mmh3`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[favfreak]] · [[BlackArch Tools]]

