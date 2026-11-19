---
tool: "githack"
build-system: "scripts"
upstream: "https://github.com/lijiejie/githack"
recipe: "blackarch"
source: "https://github.com/lijiejie/githack"
license: "custom:unknown"
blackarch-pkgver: "16.a3d70b1"
---

# Installing githack

> A `.git` folder disclosure exploit.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/githack) (version `16.a3d70b1`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S githack    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/lijiejie/githack) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[githack]] · [[BlackArch Tools]]

