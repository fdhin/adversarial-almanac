---
tool: "vulmap"
build-system: "scripts"
upstream: "https://github.com/vulmon/Vulmap"
recipe: "blackarch"
source: "https://github.com/vulmon/Vulmap"
license: "GPL"
blackarch-pkgver: "95.a167c47"
---

# Installing vulmap

> Vulmap Online Local Vulnerability Scanners Project

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/vulmap) (version `95.a167c47`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S vulmap    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/vulmon/Vulmap.git
cd Vulmap
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `requests`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[scanner]] · [[vulmap]] · [[BlackArch Tools]]

