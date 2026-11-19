---
tool: "binnavi"
build-system: "scripts"
upstream: "https://github.com/google/binnavi"
recipe: "blackarch"
aur-package: "binnavi"
source: "https://github.com/google/binnavi"
license: "Apache"
blackarch-pkgver: "6.1.0"
---

# Installing binnavi

> A binary analysis IDE that allows to inspect, navigate, edit and annotate control flow graphs and call graphs of disassembled code.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/binnavi) (version `6.1.0`, license Apache).

## Arch / BlackArch

```bash
sudo pacman -S binnavi    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/google/binnavi) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `java-environment`
- **System libraries** (Arch name — translate for your distro):
  - `postgresql`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[disassembler]] · [[binnavi]] · [[BlackArch Tools]]

