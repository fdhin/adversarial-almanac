---
tool: "fuxploider"
build-system: "scripts"
upstream: "https://github.com/almandin/fuxploider"
recipe: "blackarch"
source: "https://github.com/almandin/fuxploider"
license: "GPL-2.0-or-later"
blackarch-pkgver: "v1.0.r50.g75a81ff"
---

# Installing fuxploider

> Tool that automates the process of detecting and exploiting file upload forms flaws.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/fuxploider) (version `v1.0.r50.g75a81ff`, license GPL-2.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S fuxploider    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/almandin/fuxploider) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `beautifulsoup4`, `coloredlogs`, `requests`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[fuxploider]] · [[BlackArch Tools]]

