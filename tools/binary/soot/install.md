---
tool: "soot"
build-system: "scripts"
upstream: "http://www.sable.mcgill.ca/soot"
recipe: "blackarch"
aur-package: "soot"
source: "http://www.sable.mcgill.ca/soot"
license: "LGPL"
blackarch-pkgver: "3.4.0"
---

# Installing soot

> A Java Bytecode Analysis and Transformation Framework.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/soot) (version `3.4.0`, license LGPL).

## Arch / BlackArch

```bash
sudo pacman -S soot    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://www.sable.mcgill.ca/soot) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `java-runtime`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/java`

Backlinks: [[binary]] · [[soot]] · [[BlackArch Tools]]

