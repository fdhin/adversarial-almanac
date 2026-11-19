---
tool: "halcyon-ide"
build-system: "scripts"
upstream: "https://halcyon-ide.org/"
recipe: "blackarch"
source: "https://halcyon-ide.org/"
license: "MIT"
blackarch-pkgver: "2.0.2"
---

# Installing halcyon-ide

> First IDE for Nmap Script (NSE) Development.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/halcyon-ide) (version `2.0.2`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S halcyon-ide    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://halcyon-ide.org/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `java-environment`
- **System libraries** (Arch name — translate for your distro):
  - `bash`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[misc]] · [[halcyon-ide]] · [[BlackArch Tools]]

