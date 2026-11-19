---
tool: "jeb-mips"
build-system: "scripts"
upstream: "https://www.pnfsoftware.com/jeb/mips"
recipe: "blackarch"
source: "https://www.pnfsoftware.com/dl?jebdemomips"
license: "unknown"
blackarch-pkgver: "5.20.0.202411121942"
---

# Installing jeb-mips

> Mips decompiler.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/jeb-mips) (version `5.20.0.202411121942`, license n/a).

## Arch / BlackArch

```bash
sudo pacman -S jeb-mips    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://www.pnfsoftware.com/jeb/mips) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `java-environment`
- **System libraries** (Arch name — translate for your distro):
  - `java-runtime-common`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[reversing]] · [[jeb-mips]] · [[BlackArch Tools]]

