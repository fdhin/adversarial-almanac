---
tool: "bfac"
build-system: "scripts"
upstream: "https://github.com/mazen160/bfac"
recipe: "blackarch"
source: "https://github.com/mazen160/bfac"
license: "GPL-3.0-or-later"
blackarch-pkgver: "v1.0.r12.g18fb0b5"
---

# Installing bfac

> An automated tool that checks for backup artifacts that may disclose the web-application's source code.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/bfac) (version `v1.0.r12.g18fb0b5`, license GPL-3.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S bfac    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/mazen160/bfac) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `colorama`, `requests`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[bfac]] · [[BlackArch Tools]]

