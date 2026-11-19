---
tool: "dexpatcher"
build-system: "scripts"
upstream: "https://github.com/DexPatcher/dexpatcher-tool"
recipe: "blackarch"
aur-package: "dexpatcher"
source: "https://github.com/DexPatcher/dexpatcher-tool"
license: "GPL3"
blackarch-pkgver: "1.7.0"
---

# Installing dexpatcher

> Modify Android DEX/APK files at source-level using Java.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/dexpatcher) (version `1.7.0`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S dexpatcher    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/DexPatcher/dexpatcher-tool) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`, `java-environment`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/dexpatcher`

Backlinks: [[mobile]] · [[dexpatcher]] · [[BlackArch Tools]]

