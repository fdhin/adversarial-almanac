---
tool: "dpeparser"
build-system: "scripts"
upstream: "http://www.toolswatch.org/dpe/"
recipe: "blackarch"
source: "http://www.toolswatch.org/dpe/dpeparser.py"
license: "GPL"
blackarch-pkgver: "beta002"
---

# Installing dpeparser

> Default password enumeration project

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/dpeparser) (version `beta002`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S dpeparser    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://www.toolswatch.org/dpe/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[cracker]] · [[dpeparser]] · [[BlackArch Tools]]

