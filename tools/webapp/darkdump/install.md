---
tool: "darkdump"
build-system: "scripts"
upstream: "https://github.com/josh0xA/darkdump"
recipe: "blackarch"
source: "https://github.com/josh0xA/darkdump"
license: "MIT"
blackarch-pkgver: "Darkdump.4.r5.g59912f6"
---

# Installing darkdump

> Open Source Intelligence interface for Deep Web scraping.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/darkdump) (version `Darkdump.4.r5.g59912f6`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S darkdump    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/josh0xA/darkdump) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `pip`, `setuptools`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[darkdump]] · [[BlackArch Tools]]

