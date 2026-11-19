---
tool: "svn-extractor"
build-system: "scripts"
upstream: "https://github.com/anantshri/svn-extractor"
recipe: "blackarch"
source: "https://github.com/anantshri/svn-extractor"
license: "GPL3"
blackarch-pkgver: "45.6829804"
---

# Installing svn-extractor

> A simple script to extract all web resources by means of .SVN folder exposed over network.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/svn-extractor) (version `45.6829804`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S svn-extractor    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/anantshri/svn-extractor) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `pysqlite3`, `requests`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[svn-extractor]] · [[BlackArch Tools]]

