---
tool: "passgan"
build-system: "scripts"
upstream: "https://github.com/D3vil0p3r/PassGAN"
recipe: "blackarch"
source: "https://github.com/D3vil0p3r/PassGAN"
license: "MIT"
blackarch-pkgver: "38.5927158"
---

# Installing passgan

> A Deep Learning Approach for Password Guessing.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/passgan) (version `38.5927158`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S passgan    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/D3vil0p3r/PassGAN) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `matplotlib`, `numpy`, `tensorflow`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[cracker]] · [[passgan]] · [[BlackArch Tools]]

