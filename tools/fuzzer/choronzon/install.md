---
tool: "choronzon"
build-system: "scripts"
upstream: "https://github.com/CENSUS/choronzon"
recipe: "blackarch"
source: "https://github.com/CENSUS/choronzon"
license: "BSD"
blackarch-pkgver: "4.d702c31"
---

# Installing choronzon

> An evolutionary knowledge-based fuzzer.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/choronzon) (version `4.d702c31`, license BSD).

## Arch / BlackArch

```bash
sudo pacman -S choronzon    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/CENSUS/choronzon) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-sortedcontainers (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[fuzzer]] · [[choronzon]] · [[BlackArch Tools]]

