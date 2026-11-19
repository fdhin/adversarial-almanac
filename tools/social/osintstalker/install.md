---
tool: "osintstalker"
ebuild-package: "osintstalker"
build-system: "scripts"
upstream: "https://github.com/milo2012/osintstalker/"
recipe: "archstrike-pkgbuild"
recipe-url: "https://github.com/ArchStrike/ArchStrike/tree/master/archstrike/osintstalker"
version: "2014.1.4"
license: "custom:unknown"
---

# Installing osintstalker

> fbStalker - OSINT tool for Facebook - Based on Facebook Graph and other stuff

Recipe source of truth: ArchStrike [`PKGBUILD`](https://github.com/ArchStrike/ArchStrike/tree/master/archstrike/osintstalker) (version `2014.1.4`).

## Arch / BlackArch — via the [archstrike] repo

```bash
# enable the archstrike repo (keyring + Server line) per https://archstrike.org/setup
sudo pacman -S osintstalker
```

The same PKGBUILD is usually on the AUR (`paru -S osintstalker`).

## Any other Linux distro

**From source:**

```bash
git clone https://github.com/milo2012/osintstalker.git
cd osintstalker
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `chrome`
  - `chromium`
- ⚠️ Python 2 only deps: python2-beautifulsoup4, python2-foursquare, python2-gdata, python2-geopy, python2-google, python2-instagram, python2-oauth2, python2-pygeocoder, python2-pygoogle, python2-pysqlite, python2-python-linkedin, python2-pytz, python2-selenium, python2-setuptools, python2-termcolor, python2-twittersearch, python2-tzlocal (legacy; needs python2)

## Installed layout (per recipe)

- Commands: `/usr/bin/fbstalker`, `/usr/bin/geostalker`

Backlinks: [[social]] · [[osintstalker]] · [[ArchStrike Gap Analysis]]
