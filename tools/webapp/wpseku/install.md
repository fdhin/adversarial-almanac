---
tool: "wpseku"
build-system: "scripts"
upstream: "https://github.com/m4ll0k/WPSeku"
recipe: "blackarch"
source: "https://github.com/m4ll0k/WPSeku"
license: "custom:unknown"
blackarch-pkgver: "39.862fb2c"
---

# Installing wpseku

> Simple Wordpress Security Scanner.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/wpseku) (version `39.862fb2c`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S wpseku    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/m4ll0k/WPSeku.git
cd WPSeku
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `humanfriendly`, `requests`, `urllib3`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[wpseku]] · [[BlackArch Tools]]

