---
tool: "cms-few"
build-system: "scripts"
upstream: "http://packetstormsecurity.com/files/64722/cms_few.py.txt.html"
recipe: "blackarch"
source: "http://packetstormsecurity.com/files/64722/cms_few.py.txt.html"
license: "custom:unknown"
blackarch-pkgver: "0.1"
---

# Installing cms-few

> Joomla, Mambo, PHP-Nuke, and XOOPS CMS SQL injection vulnerability scanning tool written in Python.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/cms-few) (version `0.1`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S cms-few    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://packetstormsecurity.com/files/64722/cms_few.py.txt.html) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[cms-few]] · [[BlackArch Tools]]

