---
tool: "jbe"
build-system: "scripts"
upstream: "https://set.ee/jbe/"
recipe: "blackarch"
source: "https://set.ee/jbe/"
license: "GPL2"
blackarch-pkgver: "0.1.1"
---

# Installing jbe

> Java bytecode editor suitable for viewing and modifying java class files.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/jbe) (version `0.1.1`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S jbe    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://set.ee/jbe/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `java-environment`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[decompiler]] · [[jbe]] · [[BlackArch Tools]]

