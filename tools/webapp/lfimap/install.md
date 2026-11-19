---
tool: "lfimap"
build-system: "scripts"
upstream: "https://github.com/hansmach1ne/lfimap"
recipe: "blackarch"
source: "https://github.com/hansmach1ne/lfimap"
license: "Apache"
blackarch-pkgver: "286.ac1f29c"
---

# Installing lfimap

> Local file inclusion discovery and exploitation tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/lfimap) (version `286.ac1f29c`, license Apache).

## Arch / BlackArch

```bash
sudo pacman -S lfimap    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/hansmach1ne/lfimap) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `pybase64`, `requests`, `urllib3`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[lfimap]] · [[BlackArch Tools]]

