---
tool: "ghost-phisher"
build-system: "scripts"
upstream: "https://github.com/Obsidian-Covenant/ghost-phisher"
recipe: "blackarch"
source: "https://github.com/Obsidian-Covenant/ghost-phisher"
license: "GPL-3.0-or-later"
blackarch-pkgver: "2.0"
---

# Installing ghost-phisher

> GUI suite for phishing and penetration attacks.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/ghost-phisher) (version `2.0`, license GPL-3.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S ghost-phisher    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/Obsidian-Covenant/ghost-phisher) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `bottle`, `pyqt6`, `scapy`
- **System libraries** (Arch name — translate for your distro):
  - `aircrack-ng`
  - `python`
  - `xterm`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[ghost-phisher]] · [[BlackArch Tools]]

