---
tool: "blindsql"
build-system: "scripts"
upstream: "http://www.enye-sec.org/programas.html"
recipe: "blackarch"
source: "http://www.enye-sec.org/programas.html"
license: "GPL"
blackarch-pkgver: "1.0"
provides: ["sqlcheck", "sqldata"]
---

# Installing blindsql

> Set of bash scripts for blind SQL injection attacks.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/blindsql) (version `1.0`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S blindsql    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://www.enye-sec.org/programas.html) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `bash`

## Installed layout (per recipe)

- Commands: `/usr/bin/sqlcheck`, `/usr/bin/sqldata`

Backlinks: [[database]] · [[blindsql]] · [[BlackArch Tools]]

