---
tool: "reconscan"
build-system: "scripts"
upstream: "https://github.com/RoliSoft/ReconScan"
recipe: "blackarch"
source: "https://github.com/RoliSoft/ReconScan"
license: "GPL3"
blackarch-pkgver: "61.afbcfc0"
---

# Installing reconscan

> Network reconnaissance and vulnerability assessment tools.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/reconscan) (version `61.afbcfc0`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S reconscan    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/RoliSoft/ReconScan.git
cd ReconScan
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `colorama`, `lxml`, `python-libnmap`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python3`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[reconscan]] · [[BlackArch Tools]]

