---
tool: "bindiff"
build-system: "scripts"
upstream: "http://www.zynamics.com/bindiff.html"
recipe: "blackarch"
aur-package: "bindiff"
source: "http://www.zynamics.com/bindiff.html"
license: "custom:unknown"
blackarch-pkgver: "6.0.0"
---

# Installing bindiff

> A comparison tool for binary files, that assists vulnerability researchers and engineers to quickly find differences and similarities in disassembled code.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/bindiff) (version `6.0.0`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S bindiff    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://www.zynamics.com/bindiff.html) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `java-runtime`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[binary]] · [[bindiff]] · [[BlackArch Tools]]

