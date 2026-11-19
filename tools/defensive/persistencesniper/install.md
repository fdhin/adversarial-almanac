---
tool: "persistencesniper"
build-system: "scripts"
upstream: "https://github.com/last-byte/PersistenceSniper"
recipe: "blackarch"
source: "https://github.com/last-byte/PersistenceSniper"
license: "MIT"
blackarch-pkgver: "v1.17.1.r0.g13006aa"
---

# Installing persistencesniper

> Hunt persistences implanted in Windows machines.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/persistencesniper) (version `v1.17.1.r0.g13006aa`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S persistencesniper    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/last-byte/PersistenceSniper) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[defensive]] · [[persistencesniper]] · [[BlackArch Tools]]

