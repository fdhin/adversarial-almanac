---
tool: "gh-dork"
build-system: "scripts"
upstream: "https://github.com/molly/gh-dork"
recipe: "blackarch"
source: "https://github.com/molly/gh-dork"
license: "Apache"
blackarch-pkgver: "3.799f86f"
---

# Installing gh-dork

> Github dorking tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/gh-dork) (version `3.799f86f`, license Apache).

## Arch / BlackArch

```bash
sudo pacman -S gh-dork    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/molly/gh-dork) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `github3.py`, `pyjwt`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[gh-dork]] · [[BlackArch Tools]]

