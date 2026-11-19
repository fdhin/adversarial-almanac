---
tool: "ubitack"
build-system: "scripts"
upstream: "https://code.google.com/archive/p/ubitack/"
recipe: "blackarch"
source: "https://code.google.com/archive/p/ubitack/"
license: "GPL-3.0-or-later"
blackarch-pkgver: "0.3"
---

# Installing ubitack

> Tool, which automates some of the tasks you might need on a (wireless) penetration test or while you are on the go.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/ubitack) (version `0.3`, license GPL-3.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S ubitack    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://code.google.com/archive/p/ubitack/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `bash`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[wireless]] · [[ubitack]] · [[BlackArch Tools]]

