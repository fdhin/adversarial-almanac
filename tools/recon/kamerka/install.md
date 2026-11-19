---
tool: "kamerka"
build-system: "scripts"
upstream: "https://github.com/s7x/kamerka"
recipe: "blackarch"
source: "https://github.com/s7x/kamerka"
license: "MIT"
blackarch-pkgver: "48.1f301e8"
---

# Installing kamerka

> Build interactive map of cameras from Shodan.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/kamerka) (version `48.1f301e8`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S kamerka    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/s7x/kamerka) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `pip`, `virtualenv`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[kamerka]] · [[BlackArch Tools]]

