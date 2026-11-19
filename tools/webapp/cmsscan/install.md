---
tool: "cmsscan"
build-system: "scripts"
upstream: "https://github.com/ajinabraham/CMSScan"
recipe: "blackarch"
source: "https://github.com/ajinabraham/CMSScan"
license: "GPL3"
blackarch-pkgver: "43.f060b4b"
---

# Installing cmsscan

> CMS scanner to identify and find vulnerabilities for Wordpress, Drupal, Joomla, vBulletin.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/cmsscan) (version `43.f060b4b`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S cmsscan    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/ajinabraham/CMSScan) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `fake-useragent`, `flask`, `flask-basicauth`, `requests`, `setuptools`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `droopescan`
  - `gunicorn`
  - `joomscan`
  - `python`
  - `vbscan`
  - `wpscan`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[cmsscan]] · [[BlackArch Tools]]

