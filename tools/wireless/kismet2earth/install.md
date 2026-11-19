---
tool: "kismet2earth"
build-system: "scripts"
upstream: "https://code.google.com/archive/p/kismet2earth"
recipe: "blackarch"
source: "https://code.google.com/archive/p/kismet2earth"
license: "GPL-2.0-or-later"
blackarch-pkgver: "2.0"
provides: ["db2ge", "k2db"]
---

# Installing kismet2earth

> A set of utilities that convert from Kismet logs to Google Earth .kml format.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/kismet2earth) (version `2.0`, license GPL-2.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S kismet2earth    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://code.google.com/archive/p/kismet2earth) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `python`
  - `sqlite` — Debian libsqlite3-dev / Fedora sqlite-devel

## Installed layout (per recipe)

- Commands: `/usr/bin/db2ge`, `/usr/bin/k2db`

Backlinks: [[wireless]] · [[kismet2earth]] · [[BlackArch Tools]]

