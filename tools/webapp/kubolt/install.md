---
tool: "kubolt"
build-system: "scripts"
upstream: "https://github.com/averonesis/kubolt"
recipe: "blackarch"
source: "https://github.com/averonesis/kubolt"
license: "GPL3"
blackarch-pkgver: "28.0027239"
---

# Installing kubolt

> Utility for scanning public kubernetes clusters.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/kubolt) (version `28.0027239`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S kubolt    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/averonesis/kubolt) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `click`, `requests`, `shodan`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[kubolt]] · [[BlackArch Tools]]

