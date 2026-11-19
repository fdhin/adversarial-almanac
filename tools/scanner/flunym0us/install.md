---
tool: "flunym0us"
build-system: "scripts"
upstream: "https://code.google.com/archive/p/flunym0us/"
recipe: "blackarch"
source: "https://code.google.com/archive/p/flunym0us/"
license: "GPL-3.0-or-later"
blackarch-pkgver: "2.0"
---

# Installing flunym0us

> A Vulnerability Scanner for Wordpress and Moodle.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/flunym0us) (version `2.0`, license GPL-3.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S flunym0us    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://code.google.com/archive/p/flunym0us/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[flunym0us]] · [[BlackArch Tools]]

