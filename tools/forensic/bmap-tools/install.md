---
tool: "bmap-tools"
build-system: "scripts"
upstream: "http://git.infradead.org/users/dedekind/bmap-tools.git"
recipe: "blackarch"
source: "http://git.infradead.org/users/dedekind/bmap-tools.git"
license: "GPL2"
blackarch-pkgver: "3.7"
---

# Installing bmap-tools

> Tool for copying largely sparse files using information from a block map file.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/bmap-tools) (version `3.7`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S bmap-tools    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://git.infradead.org/users/dedekind/bmap-tools.git) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-setuptools (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/man`

Backlinks: [[forensic]] · [[bmap-tools]] · [[BlackArch Tools]]

