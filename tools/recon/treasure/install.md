---
tool: "treasure"
build-system: "scripts"
upstream: "https://github.com/GuerrillaWarfare/Treasure"
recipe: "blackarch"
source: "https://github.com/GuerrillaWarfare/Treasure"
license: "custom:unknown"
blackarch-pkgver: "2.b3249be"
---

# Installing treasure

> Hunt for sensitive information through githubs code search.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/treasure) (version `2.b3249be`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S treasure    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/GuerrillaWarfare/Treasure) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-beautifulsoup4, python2-requests (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[treasure]] · [[BlackArch Tools]]

