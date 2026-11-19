---
tool: "canalyzat0r"
build-system: "scripts"
upstream: "https://github.com/schutzwerk/CANalyzat0r"
recipe: "blackarch"
source: "https://github.com/schutzwerk/CANalyzat0r"
license: "GPL-3.0-or-later"
blackarch-pkgver: "41.6bc251e"
---

# Installing canalyzat0r

> Security analysis toolkit for proprietary car protocols.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/canalyzat0r) (version `41.6bc251e`, license GPL-3.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S canalyzat0r    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/schutzwerk/CANalyzat0r.git
cd CANalyzat0r
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `aenum`, `can`, `pyside`, `pyvit`, `sphinx_rtd_theme`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `can-utils`
  - `ffmpeg`
  - `iproute2`
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[automobile]] · [[canalyzat0r]] · [[BlackArch Tools]]

