---
tool: "joomlascan"
build-system: "scripts"
upstream: "http://packetstormsecurity.com/files/62126/joomlascan-1.2.py.txt.html"
recipe: "blackarch"
source: "http://packetstormsecurity.com/files/62126/joomlascan-1.2.py.txt.html"
license: "custom:unknown"
blackarch-pkgver: "1.2"
---

# Installing joomlascan

> Joomla scanner scans for known vulnerable remote file inclusion paths and files.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/joomlascan) (version `1.2`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S joomlascan    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://packetstormsecurity.com/files/62126/joomlascan.2.py.txt.html) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-httplib2 (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[joomlascan]] · [[BlackArch Tools]]

