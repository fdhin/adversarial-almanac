---
tool: "dsxs"
build-system: "scripts"
upstream: "https://github.com/stamparm/DSXS"
recipe: "blackarch"
source: "https://github.com/stamparm/DSXS"
license: "custom:unknown"
blackarch-pkgver: "136.f5eb0be"
---

# Installing dsxs

> A fully functional Cross-site scripting vulnerability scanner (supporting GET and POST parameters) written in under 100 lines of code.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/dsxs) (version `136.f5eb0be`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S dsxs    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/stamparm/DSXS.git
cd DSXS
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[dsxs]] · [[BlackArch Tools]]

