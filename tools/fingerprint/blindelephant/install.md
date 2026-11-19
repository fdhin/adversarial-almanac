---
tool: "blindelephant"
build-system: "scripts"
upstream: "https://blindelephant.sourceforge.net/"
recipe: "blackarch"
source: "https://blindelephant.sourceforge.net/"
license: "LGPL-3.0-or-later"
blackarch-pkgver: "8"
provides: ["BlindElephant.py"]
---

# Installing blindelephant

> A web application fingerprinter. Attempts to discover the version of a (known) web application by comparing static files at known locations.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/blindelephant) (version `8`, license LGPL-3.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S blindelephant    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://blindelephant.sourceforge.net/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `python2`
  - `subversion`

## Installed layout (per recipe)

- Commands: `/usr/bin/BlindElephant.py`

Backlinks: [[fingerprint]] · [[blindelephant]] · [[BlackArch Tools]]

