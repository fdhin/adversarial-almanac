---
tool: "luyten"
build-system: "scripts"
upstream: "https://github.com/deathmarine/Luyten"
recipe: "blackarch"
aur-package: "luyten"
source: "https://github.com/deathmarine/Luyten"
license: "APACHE"
blackarch-pkgver: "0.5.4"
---

# Installing luyten

> An Open Source Java Decompiler Gui for Procyon.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/luyten) (version `0.5.4`, license APACHE).

## Arch / BlackArch

```bash
sudo pacman -S luyten    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/deathmarine/Luyten) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`, `java-environment`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[decompiler]] · [[luyten]] · [[BlackArch Tools]]

