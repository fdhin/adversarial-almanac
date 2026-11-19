---
tool: "fbid"
build-system: "scripts"
upstream: "https://github.com/guelfoweb/fbid"
recipe: "blackarch"
source: "https://github.com/guelfoweb/fbid"
license: "GPL3"
blackarch-pkgver: "16.1b35eb9"
---

# Installing fbid

> Show info about the author by facebook photo url.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/fbid) (version `16.1b35eb9`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S fbid    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/guelfoweb/fbid) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-simplejson (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[fbid]] · [[BlackArch Tools]]

