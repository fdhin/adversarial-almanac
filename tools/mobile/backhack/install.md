---
tool: "backhack"
build-system: "scripts"
upstream: "https://github.com/l0gan/backHack"
recipe: "blackarch"
source: "https://github.com/l0gan/backHack"
license: "Apache"
blackarch-pkgver: "39.561ec86"
---

# Installing backhack

> Tool to perform Android app analysis by backing up and extracting apps, allowing you to analyze and modify file system contents for apps.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/backhack) (version `39.561ec86`, license Apache).

## Arch / BlackArch

```bash
sudo pacman -S backhack    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/l0gan/backHack.git
cd backHack
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-pytz (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[mobile]] · [[backhack]] · [[BlackArch Tools]]

