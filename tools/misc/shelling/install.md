---
tool: "shelling"
build-system: "scripts"
upstream: "https://github.com/ewilded/shelling"
recipe: "blackarch"
source: "https://github.com/ewilded/shelling"
license: "custom:unknown"
blackarch-pkgver: "227.0a6c135"
---

# Installing shelling

> An offensive approach to the anatomy of improperly written OS command injection sanitisers.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/shelling) (version `227.0a6c135`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S shelling    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/ewilded/shelling) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`, `java-environment`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[misc]] · [[shelling]] · [[BlackArch Tools]]

