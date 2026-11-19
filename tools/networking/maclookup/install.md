---
tool: "maclookup"
build-system: "scripts"
upstream: "https://github.com/paraxor/maclookup"
recipe: "blackarch"
source: "https://github.com/paraxor/maclookup"
license: "GPL3"
blackarch-pkgver: "0.4"
---

# Installing maclookup

> Lookup MAC addresses in the IEEE MA-L/OUI public listing.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/maclookup) (version `0.4`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S maclookup    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/paraxor/maclookup) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/maclookup`

Backlinks: [[networking]] · [[maclookup]] · [[BlackArch Tools]]

