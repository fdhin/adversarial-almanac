---
tool: "tinfoleak2"
build-system: "scripts"
upstream: "http://www.vicenteaguileradiaz.com/tools/"
recipe: "blackarch"
source: "http://www.vicenteaguileradiaz.com/tools/"
license: "custom:unknown"
blackarch-pkgver: "41.c45c33e"
---

# Installing tinfoleak2

> The most complete open-source tool for Twitter intelligence analysis.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/tinfoleak2) (version `41.c45c33e`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S tinfoleak2    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/vaguileradiaz/tinfoleak.git
cd tinfoleak
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `exifread`, `exiv2`, `jinja`, `oauth2`, `pillow`, `pyopenssl`, `tweepy`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[tinfoleak2]] · [[BlackArch Tools]]

