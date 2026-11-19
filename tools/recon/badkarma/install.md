---
tool: "badkarma"
build-system: "scripts"
upstream: "https://github.com/r3vn/badKarma"
recipe: "blackarch"
source: "https://github.com/r3vn/badKarma"
license: "GPL-3.0-or-later"
blackarch-pkgver: "85.2c46334"
---

# Installing badkarma

> Advanced network reconnaissance toolkit.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/badkarma) (version `85.2c46334`, license GPL-3.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S badkarma    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/r3vn/badKarma.git
cd badKarma
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `gobject`, `python-libnmap`, `shodan`, `sqlalchemy`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `ffmpeg`
  - `gtksourceview3`
  - `mitmproxy`
  - `osm-gps-map`
  - `phantomjs`
  - `python`
  - `vte3`
  - `webkit2gtk`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[badkarma]] · [[BlackArch Tools]]

