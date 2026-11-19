---
tool: "github-dorks"
build-system: "scripts"
upstream: "https://github.com/techgaun/github-dorks"
recipe: "blackarch"
source: "https://github.com/techgaun/github-dorks"
license: "Apache"
blackarch-pkgver: "106.16dc173"
---

# Installing github-dorks

> Collection of github dorks and helper tool to automate the process of checking dorks.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/github-dorks) (version `106.16dc173`, license Apache).

## Arch / BlackArch

```bash
sudo pacman -S github-dorks    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/techgaun/github-dorks) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `deprecated`, `feedparser`, `github3.py`, `pyjwt`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[github-dorks]] · [[BlackArch Tools]]

