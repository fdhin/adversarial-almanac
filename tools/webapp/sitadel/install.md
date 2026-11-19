---
tool: "sitadel"
build-system: "scripts"
upstream: "https://github.com/shenril/Sitadel"
recipe: "blackarch"
source: "https://github.com/shenril/Sitadel"
license: "GPL3"
blackarch-pkgver: "199.077c4e4"
---

# Installing sitadel

> Web Application Security Scanner.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/sitadel) (version `199.077c4e4`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S sitadel    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/shenril/Sitadel.git
cd Sitadel
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `colorama`, `requests`, `urllib3`, `yaml`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `scrapy`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[sitadel]] · [[BlackArch Tools]]

