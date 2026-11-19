---
tool: "fimap"
build-system: "scripts"
upstream: "https://gitlab.tha-imax.de/root/fimap"
recipe: "blackarch"
source: "https://gitlab.tha-imax.de/root/fimap"
license: "GPL-2.0-or-later"
blackarch-pkgver: "230.41eca15"
---

# Installing fimap

> A little tool for local and remote file inclusion auditing and exploitation.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/fimap) (version `230.41eca15`, license GPL-2.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S fimap    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://gitlab.tha-imax.de/root/fimap) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `urllib3`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-httplib2 (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/fimap`

Backlinks: [[webapp]] · [[fimap]] · [[BlackArch Tools]]

