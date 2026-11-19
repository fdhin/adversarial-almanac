---
tool: "recentfilecache-parser"
build-system: "scripts"
upstream: "https://github.com/prolsen/recentfilecache-parser"
recipe: "blackarch"
source: "https://github.com/prolsen/recentfilecache-parser"
license: "custom:unknown"
blackarch-pkgver: "2.5e22518"
---

# Installing recentfilecache-parser

> Python parser for the RecentFileCache.bcf on Windows.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/recentfilecache-parser) (version `2.5e22518`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S recentfilecache-parser    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/prolsen/recentfilecache-parser) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[forensic]] · [[recentfilecache-parser]] · [[BlackArch Tools]]

