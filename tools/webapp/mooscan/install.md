---
tool: "mooscan"
build-system: "scripts"
upstream: "https://github.com/vortexau/mooscan"
recipe: "blackarch"
source: "https://github.com/vortexau/mooscan"
license: "custom:unknown"
blackarch-pkgver: "10.82963b0"
---

# Installing mooscan

> A scanner for Moodle LMS.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/mooscan) (version `10.82963b0`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S mooscan    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/vortexau/mooscan) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `gitpython`, `pytest`, `requests`, `sqlalchemy`, `urllib3`, `yaml`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[mooscan]] · [[BlackArch Tools]]

