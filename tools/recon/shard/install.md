---
tool: "shard"
build-system: "scripts"
upstream: "https://github.com/philwantsfish/shard/releases"
recipe: "blackarch"
aur-package: "shard"
source: "https://github.com/philwantsfish/shard/releases"
license: "MIT"
blackarch-pkgver: "1.5"
---

# Installing shard

> A command line tool to detect shared passwords.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/shard) (version `1.5`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S shard    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/philwantsfish/shard) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`, `java-environment`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[shard]] · [[BlackArch Tools]]

