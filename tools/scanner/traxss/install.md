---
tool: "traxss"
build-system: "scripts"
upstream: "https://github.com/M4cs/traxss"
recipe: "blackarch"
source: "https://github.com/M4cs/traxss"
license: "MIT"
blackarch-pkgver: "81.48dee2e"
---

# Installing traxss

> Automated XSS Vulnerability Scanner.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/traxss) (version `81.48dee2e`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S traxss    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/M4cs/traxss) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `crayons`, `requests`, `selenium`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[traxss]] · [[BlackArch Tools]]

