---
tool: "facebot"
build-system: "scripts"
upstream: "https://github.com/pun1sh3r/facebot"
recipe: "blackarch"
source: "https://github.com/pun1sh3r/facebot"
license: "GPL2"
blackarch-pkgver: "23.57f6025"
---

# Installing facebot

> A facebook profile and reconnaissance system.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/facebot) (version `23.57f6025`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S facebot    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/pun1sh3r/facebot) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `cookiejar`
  - `mysql-python`
  - `python2`
- ⚠️ Python 2 only deps: python2-beautifulsoup4, python2-binaryornot, python2-configparser, python2-cookiecutter, python2-facepy, python2-gdata, python2-jinja, python2-markupsafe, python2-multipartposthandler, python2-pager, python2-setuptools, python2-six, python2-wsgiref, python2-yaml (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[facebot]] · [[BlackArch Tools]]

