---
tool: "chaosmap"
build-system: "scripts"
upstream: "http://freecode.com/projects/chaosmap"
recipe: "blackarch"
source: "http://freecode.com/projects/chaosmap"
license: "GPL"
blackarch-pkgver: "1.3"
---

# Installing chaosmap

> An information gathering tool and dns / whois / web server scanner

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/chaosmap) (version `1.3`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S chaosmap    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://freecode.com/projects/chaosmap) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[forensic]] · [[chaosmap]] · [[BlackArch Tools]]

