---
tool: "adminpagefinder"
build-system: "scripts"
upstream: "http://packetstormsecurity.com/files/112855/Admin-Page-Finder-Script.html"
recipe: "blackarch"
source: "http://packetstormsecurity.com/files/112855/Admin-Page-Finder-Script.html"
license: "custom:unknown"
blackarch-pkgver: "0.1"
---

# Installing adminpagefinder

> This python script looks for a large amount of possible administrative interfaces on a given site.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/adminpagefinder) (version `0.1`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S adminpagefinder    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://packetstormsecurity.com/files/112855/Admin-Page-Finder-Script.html) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[adminpagefinder]] · [[BlackArch Tools]]

