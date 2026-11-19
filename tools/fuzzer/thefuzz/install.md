---
tool: "thefuzz"
build-system: "scripts"
upstream: "https://github.com/droberson/thefuzz"
recipe: "blackarch"
source: "https://github.com/droberson/thefuzz"
license: "MIT"
blackarch-pkgver: "160.b4c2c80"
---

# Installing thefuzz

> CLI fuzzing tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/thefuzz) (version `160.b4c2c80`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S thefuzz    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/droberson/thefuzz) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-argparse, python2-subprocess32 (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[fuzzer]] · [[thefuzz]] · [[BlackArch Tools]]

