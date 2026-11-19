---
tool: "uatester"
build-system: "scripts"
upstream: "https://code.google.com/archive/p/ua-tester/"
recipe: "blackarch"
source: "https://code.google.com/archive/p/ua-tester/"
license: "BSD-3-Clause"
blackarch-pkgver: "1.06"
---

# Installing uatester

> User Agent String Tester.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/uatester) (version `1.06`, license BSD-3-Clause).

## Arch / BlackArch

```bash
sudo pacman -S uatester    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://code.google.com/archive/p/ua-tester/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[misc]] · [[uatester]] · [[BlackArch Tools]]

