---
tool: "kismon"
build-system: "python"
upstream: "https://www.salecker.org/software/kismon.html"
recipe: "blackarch"
aur-package: "kismon"
source: "https://www.salecker.org/software/kismon.html"
license: "BSD"
blackarch-pkgver: "1.0.3"
---

# Installing kismon

> GUI client for kismet (wireless scanner/sniffer/monitor).

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/kismon) (version `1.0.3`, license BSD).

## Arch / BlackArch

```bash
sudo pacman -S kismon    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://www.salecker.org/software/kismon.html) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `cairo`, `gobject`, `kismet-rest`, `lxml`, `requests`, `setuptools`, `simplejson`
- **System libraries** (Arch name — translate for your distro):
  - `gobject-introspection-runtime`
  - `gtk3` — Debian libgtk-3-dev / Fedora gtk3-devel
  - `kismet`
  - `osm-gps-map`
  - `python`

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  python setup.py build
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[wireless]] · [[kismon]] · [[BlackArch Tools]]

