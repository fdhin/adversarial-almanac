---
tool: "crabstick"
build-system: "scripts"
upstream: "https://github.com/Hack-Hut/CrabStick"
recipe: "blackarch"
source: "https://github.com/Hack-Hut/CrabStick"
license: "custom:unknown"
blackarch-pkgver: "47.bb7827f"
---

# Installing crabstick

> Automatic remote/local file inclusion vulnerability analysis and exploit tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/crabstick) (version `47.bb7827f`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S crabstick    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/Hack-Hut/CrabStick.git
cd CrabStick
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `beautifulsoup4`, `docopt`, `paramiko`, `requests`, `urllib3`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[crabstick]] · [[BlackArch Tools]]

