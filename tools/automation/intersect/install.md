---
tool: "intersect"
build-system: "scripts"
upstream: "https://github.com/ohdae/Intersect-2.5"
recipe: "blackarch"
source: "https://github.com/ohdae/Intersect-2.5"
license: "custom"
blackarch-pkgver: "2.5"
---

# Installing intersect

> Post-exploitation framework.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/intersect) (version `2.5`, license custom).

## Arch / BlackArch

```bash
sudo pacman -S intersect    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/ohdae/Intersect.5) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/Intersect`

Backlinks: [[automation]] · [[intersect]] · [[BlackArch Tools]]

