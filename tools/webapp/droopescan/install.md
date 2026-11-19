---
tool: "droopescan"
build-system: "python"
upstream: "https://github.com/droope/droopescan"
recipe: "blackarch"
source: "https://github.com/droope/droopescan"
license: "AGPL-3.0-only"
blackarch-pkgver: "1.45.1"
---

# Installing droopescan

> A plugin-based scanner that aids security researchers in identifying issues with several CMSs, mainly Drupal & Silverstripe.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/droopescan) (version `1.45.1`, license AGPL-3.0-only).

## Arch / BlackArch

```bash
sudo pacman -S droopescan    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/droope/droopescan) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `beautifulsoup4`, `cement`, `dateutil`, `lxml`, `mock`, `nose`, `pystache`, `requests`, `simplejson`, `twisted`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  python setup.py build
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[droopescan]] · [[BlackArch Tools]]

