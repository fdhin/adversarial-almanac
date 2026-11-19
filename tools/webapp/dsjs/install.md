---
tool: "dsjs"
build-system: "scripts"
upstream: "https://github.com/stamparm/DSJS"
recipe: "blackarch"
source: "https://github.com/stamparm/DSJS"
license: "custom:unknown"
blackarch-pkgver: "33.e8af886"
---

# Installing dsjs

> A fully functional JavaScript library vulnerability scanner written in under 100 lines of code.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/dsjs) (version `33.e8af886`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S dsjs    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/stamparm/DSJS.git
cd DSJS
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `simplejson`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[dsjs]] · [[BlackArch Tools]]

