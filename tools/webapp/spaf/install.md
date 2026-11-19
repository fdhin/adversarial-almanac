---
tool: "spaf"
build-system: "scripts"
upstream: "https://github.com/Ganapati/spaf"
recipe: "blackarch"
source: "https://github.com/Ganapati/spaf"
license: "custom:unknown"
blackarch-pkgver: "11.671a976"
---

# Installing spaf

> Static Php Analysis and Fuzzer.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/spaf) (version `11.671a976`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S spaf    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/Ganapati/spaf) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-requests (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[spaf]] · [[BlackArch Tools]]

