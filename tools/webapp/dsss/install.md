---
tool: "dsss"
build-system: "scripts"
upstream: "https://github.com/stamparm/DSSS"
recipe: "blackarch"
source: "https://github.com/stamparm/DSSS"
license: "custom:unknown"
blackarch-pkgver: "128.13002c3"
---

# Installing dsss

> A fully functional SQL injection vulnerability scanner (supporting GET and POST parameters) written in under 100 lines of code.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/dsss) (version `128.13002c3`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S dsss    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/stamparm/DSSS.git
cd DSSS
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[dsss]] · [[BlackArch Tools]]

