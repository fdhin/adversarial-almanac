---
tool: "visql"
build-system: "scripts"
upstream: "https://github.com/blackvkng/viSQL"
recipe: "blackarch"
source: "https://github.com/blackvkng/viSQL"
license: "MIT"
blackarch-pkgver: "49.3082e30"
---

# Installing visql

> Scan SQL vulnerability on target site and sites of on server.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/visql) (version `49.3082e30`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S visql    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/blackvkng/viSQL.git
cd viSQL
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-colorama, python2-requests (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[visql]] · [[BlackArch Tools]]

