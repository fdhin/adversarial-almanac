---
tool: "dsfs"
build-system: "scripts"
upstream: "https://github.com/stamparm/DSFS"
recipe: "blackarch"
source: "https://github.com/stamparm/DSFS"
license: "custom:unknown"
blackarch-pkgver: "36.8e9f8e9"
---

# Installing dsfs

> A fully functional File inclusion vulnerability scanner (supporting GET and POST parameters) written in under 100 lines of code.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/dsfs) (version `36.8e9f8e9`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S dsfs    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/stamparm/DSFS.git
cd DSFS
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[dsfs]] · [[BlackArch Tools]]

