---
tool: "urldigger"
build-system: "scripts"
upstream: "https://code.google.com/archive/p/urldigger/"
recipe: "blackarch"
source: "https://code.google.com/archive/p/urldigger/"
license: "MIT"
blackarch-pkgver: "02c"
---

# Installing urldigger

> A python tool to extract URL addresses from different HOT sources and/or detect SPAM and malicious code

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/urldigger) (version `02c`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S urldigger    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://code.google.com/archive/p/urldigger/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[urldigger]] · [[BlackArch Tools]]

