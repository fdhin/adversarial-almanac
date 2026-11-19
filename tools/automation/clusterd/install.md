---
tool: "clusterd"
build-system: "scripts"
upstream: "https://github.com/hatRiot/clusterd"
recipe: "blackarch"
source: "https://github.com/hatRiot/clusterd"
license: "MIT"
blackarch-pkgver: "143.d190b2c"
---

# Installing clusterd

> Automates the fingerprinting, reconnaissance, and exploitation phases of an application server attack.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/clusterd) (version `143.d190b2c`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S clusterd    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/hatRiot/clusterd) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`, `java-environment`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-requests (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[automation]] · [[clusterd]] · [[BlackArch Tools]]

