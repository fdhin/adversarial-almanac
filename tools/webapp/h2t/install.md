---
tool: "h2t"
build-system: "scripts"
upstream: "https://github.com/gildasio/h2t"
recipe: "blackarch"
source: "https://github.com/gildasio/h2t"
license: "MIT"
blackarch-pkgver: "38.56e1447"
---

# Installing h2t

> Scans a website and suggests security headers to apply.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/h2t) (version `38.56e1447`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S h2t    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/gildasio/h2t) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `certifi`, `chardet`, `colorama`, `idna`, `requests`, `urllib3`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[h2t]] · [[BlackArch Tools]]

