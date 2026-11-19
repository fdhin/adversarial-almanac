---
tool: "geoipgen"
build-system: "scripts"
upstream: "https://code.google.com/archive/p/geoipgen/"
recipe: "blackarch"
source: "https://code.google.com/archive/p/geoipgen/"
license: "GPL-1.0-or-later"
blackarch-pkgver: "0.4"
---

# Installing geoipgen

> A country to IP addresses generator.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/geoipgen) (version `0.4`, license GPL-1.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S geoipgen    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://code.google.com/archive/p/geoipgen/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `ruby`
  - `ruby-getoptlong`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[misc]] · [[geoipgen]] · [[BlackArch Tools]]

