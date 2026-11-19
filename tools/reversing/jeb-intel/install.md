---
tool: "jeb-intel"
build-system: "scripts"
upstream: "https://www.pnfsoftware.com/jeb/intel"
recipe: "blackarch"
source: "https://www.pnfsoftware.com/dl?jebdemox86"
license: "unknown"
blackarch-pkgver: "3.0.3.201809240610"
---

# Installing jeb-intel

> Intel decompiler.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/jeb-intel) (version `3.0.3.201809240610`, license n/a).

## Arch / BlackArch

```bash
sudo pacman -S jeb-intel    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://www.pnfsoftware.com/jeb/intel) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `java-environment`
- **System libraries** (Arch name — translate for your distro):
  - `java-runtime-common`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[reversing]] · [[jeb-intel]] · [[BlackArch Tools]]

