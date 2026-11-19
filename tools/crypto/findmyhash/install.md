---
tool: "findmyhash"
build-system: "scripts"
upstream: "https://code.google.com/archive/p/findmyhash/"
recipe: "blackarch"
source: "https://code.google.com/archive/p/findmyhash/"
license: "GPL3"
blackarch-pkgver: "1.1.2"
---

# Installing findmyhash

> Crack different types of hashes using free online services.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/findmyhash) (version `1.1.2`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S findmyhash    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://code.google.com/archive/p/findmyhash/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-httplib2 (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[crypto]] · [[findmyhash]] · [[BlackArch Tools]]

