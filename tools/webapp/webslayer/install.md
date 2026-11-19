---
tool: "webslayer"
build-system: "scripts"
upstream: "https://code.google.com/archive/p/webslayer/"
recipe: "blackarch"
source: "https://storage.googleapis.com/google-code-archive-source/v2/code.google.com/webslayer/source-archive.zip"
license: "LGPL-3.0-or-later"
blackarch-pkgver: "5"
---

# Installing webslayer

> A tool designed for brute forcing Web Applications.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/webslayer) (version `5`, license LGPL-3.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S webslayer    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
curl -fLO https://storage.googleapis.com/google-code-archive-source/v2/code.google.com/webslayer/source-archive.zip
tar xf source-archive.zip
cd source-archive
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- ⚠️ Python 2 only deps: python2-pycurl, python2-pyqt4 (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/webslayer`

Backlinks: [[webapp]] · [[webslayer]] · [[BlackArch Tools]]

