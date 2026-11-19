---
tool: "jooforce"
build-system: "scripts"
upstream: "https://github.com/rastating/jooforce"
recipe: "blackarch"
source: "https://github.com/rastating/jooforce"
license: "GPL3"
blackarch-pkgver: "11.43c21ad"
---

# Installing jooforce

> A Joomla password brute force tester.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/jooforce) (version `11.43c21ad`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S jooforce    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/rastating/jooforce) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[jooforce]] · [[BlackArch Tools]]

