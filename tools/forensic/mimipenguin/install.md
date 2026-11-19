---
tool: "mimipenguin"
build-system: "scripts"
upstream: "https://github.com/huntergregal/mimipenguin"
recipe: "blackarch"
source: "https://github.com/huntergregal/mimipenguin"
license: "custom:unknown"
blackarch-pkgver: "153.ecb2ffc"
---

# Installing mimipenguin

> A tool to dump the login password from the current linux user.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/mimipenguin) (version `153.ecb2ffc`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S mimipenguin    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/huntergregal/mimipenguin) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[forensic]] · [[mimipenguin]] · [[BlackArch Tools]]

