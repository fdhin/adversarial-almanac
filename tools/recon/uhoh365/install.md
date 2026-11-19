---
tool: "uhoh365"
build-system: "scripts"
upstream: "https://github.com/Raikia/UhOh365"
recipe: "blackarch"
source: "https://github.com/Raikia/UhOh365"
license: "GPL-3.0-or-later"
blackarch-pkgver: "26.110277a"
---

# Installing uhoh365

> Script to enumerate Office 365 users without performing login attempts

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/uhoh365) (version `26.110277a`, license GPL-3.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S uhoh365    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/Raikia/UhOh365) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `requests`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[uhoh365]] · [[BlackArch Tools]]

