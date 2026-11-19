---
tool: "riwifshell"
build-system: "scripts"
upstream: "https://github.com/graniet/riwifshell"
recipe: "blackarch"
source: "https://github.com/graniet/riwifshell"
license: "custom:unknown"
blackarch-pkgver: "38.40075d5"
---

# Installing riwifshell

> Web backdoor - infector - explorer.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/riwifshell) (version `38.40075d5`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S riwifshell    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/graniet/riwifshell) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[riwifshell]] · [[BlackArch Tools]]

