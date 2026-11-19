---
tool: "jeb-android"
build-system: "scripts"
upstream: "https://www.pnfsoftware.com/jeb/android"
recipe: "blackarch"
source: "https://www.pnfsoftware.com/dl?jebdemoandroid"
license: "unknown"
blackarch-pkgver: "3.7.0.201909272058"
---

# Installing jeb-android

> Android decompiler.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/jeb-android) (version `3.7.0.201909272058`, license n/a).

## Arch / BlackArch

```bash
sudo pacman -S jeb-android    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://www.pnfsoftware.com/jeb/android) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `java-environment`
- **System libraries** (Arch name — translate for your distro):
  - `java-runtime-common`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[reversing]] · [[jeb-android]] · [[BlackArch Tools]]

