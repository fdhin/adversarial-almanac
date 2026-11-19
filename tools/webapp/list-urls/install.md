---
tool: "list-urls"
build-system: "scripts"
upstream: "http://www.whoppix.net/"
recipe: "blackarch"
source: "http://www.whoppix.net/"
license: "Python"
blackarch-pkgver: "0.1"
---

# Installing list-urls

> Extracts links from webpage.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/list-urls) (version `0.1`, license Python).

## Arch / BlackArch

```bash
sudo pacman -S list-urls    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://www.whoppix.net/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[list-urls]] · [[BlackArch Tools]]

