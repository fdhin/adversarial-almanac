---
tool: "barmie"
build-system: "scripts"
upstream: "https://github.com/NickstaDB/BaRMIe"
recipe: "blackarch"
source: "https://github.com/NickstaDB/BaRMIe"
license: "MIT"
blackarch-pkgver: "1.01"
---

# Installing barmie

> Java RMI enumeration and attack tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/barmie) (version `1.01`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S barmie    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/NickstaDB/BaRMIe) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `java-environment`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[barmie]] · [[BlackArch Tools]]

