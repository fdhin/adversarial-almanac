---
tool: "gtp-scan"
build-system: "scripts"
upstream: "http://www.c0decafe.de/"
recipe: "blackarch"
source: "http://www.c0decafe.de/"
license: "custom:unknown"
blackarch-pkgver: "0.7"
---

# Installing gtp-scan

> A small python script that scans for GTP (GPRS tunneling protocol) speaking hosts.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/gtp-scan) (version `0.7`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S gtp-scan    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://www.c0decafe.de/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-ipy (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[gtp-scan]] · [[BlackArch Tools]]

