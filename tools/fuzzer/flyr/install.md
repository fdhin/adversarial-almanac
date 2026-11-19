---
tool: "flyr"
build-system: "other"
upstream: "https://github.com/zznop/flyr"
recipe: "blackarch"
source: "https://github.com/zznop/flyr"
license: "MIT"
blackarch-pkgver: "76.4926ecc"
---

# Installing flyr

> Block-based software vulnerability fuzzing framework.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/flyr) (version `76.4926ecc`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S flyr    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/zznop/flyr) (the recipe uses a versioned archive).

```bash
# see exact recipe steps below
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `scons`

## Exact build steps from the recipe

```bash
cd $pkgname

  scons
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[fuzzer]] · [[flyr]] · [[BlackArch Tools]]

