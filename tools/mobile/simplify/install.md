---
tool: "simplify"
build-system: "scripts"
upstream: "https://github.com/CalebFenton/simplify/releases"
recipe: "blackarch"
aur-package: "simplify"
source: "https://github.com/CalebFenton/simplify/releases"
license: "GPL3"
blackarch-pkgver: "1.3.0"
---

# Installing simplify

> Generic Android Deobfuscator.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/simplify) (version `1.3.0`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S simplify    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/CalebFenton/simplify) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`, `java-environment`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[mobile]] · [[simplify]] · [[BlackArch Tools]]

