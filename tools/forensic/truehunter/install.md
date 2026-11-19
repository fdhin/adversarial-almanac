---
tool: "truehunter"
build-system: "scripts"
upstream: "https://github.com/adoreste/truehunter"
recipe: "blackarch"
source: "https://github.com/adoreste/truehunter"
license: "custom:unknown"
blackarch-pkgver: "14.0a2895d"
---

# Installing truehunter

> Detect TrueCrypt containers using a fast and memory efficient approach.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/truehunter) (version `14.0a2895d`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S truehunter    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/adoreste/truehunter) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-argparse, python2-pysqlite (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[forensic]] · [[truehunter]] · [[BlackArch Tools]]

