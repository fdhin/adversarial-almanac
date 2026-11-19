---
tool: "xsscon"
build-system: "scripts"
upstream: "https://github.com/menkrep1337/XSSCon"
recipe: "blackarch"
source: "https://github.com/menkrep1337/XSSCon"
license: "MIT"
blackarch-pkgver: "45.ce91fd6"
---

# Installing xsscon

> Simple XSS Scanner tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/xsscon) (version `45.ce91fd6`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S xsscon    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/menkrep1337/XSSCon.git
cd XSSCon
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `beautifulsoup4`, `requests`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[xsscon]] · [[BlackArch Tools]]

