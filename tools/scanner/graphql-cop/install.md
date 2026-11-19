---
tool: "graphql-cop"
build-system: "scripts"
upstream: "https://github.com/dolevf/graphql-cop"
recipe: "blackarch"
source: "https://github.com/dolevf/graphql-cop"
license: "MIT"
blackarch-pkgver: "1.16.r0.g2b7e086"
---

# Installing graphql-cop

> GraphQL vulnerability scanner.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/graphql-cop) (version `1.16.r0.g2b7e086`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S graphql-cop    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/dolevf/graphql-cop) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `requests`, `simplejson`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[graphql-cop]] · [[BlackArch Tools]]

