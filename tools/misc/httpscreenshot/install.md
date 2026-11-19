---
tool: "httpscreenshot"
build-system: "scripts"
upstream: "https://github.com/breenmachine/httpscreenshot"
recipe: "blackarch"
source: "https://github.com/breenmachine/httpscreenshot"
license: "custom:unknown"
blackarch-pkgver: "70.f081c69"
---

# Installing httpscreenshot

> A tool for grabbing screenshots and HTML of large numbers of websites.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/httpscreenshot) (version `70.f081c69`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S httpscreenshot    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/breenmachine/httpscreenshot) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `beautifulsoup4`, `lxml`, `m2crypto`, `pillow`, `python-libnmap`, `pyvirtualdisplay`, `requests`, `selenium`, `webdriver-manager`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[misc]] · [[httpscreenshot]] · [[BlackArch Tools]]

