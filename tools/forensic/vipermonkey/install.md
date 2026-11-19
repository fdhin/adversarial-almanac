---
tool: "vipermonkey"
build-system: "scripts"
upstream: "https://github.com/decalage2/ViperMonkey"
recipe: "blackarch"
source: "https://github.com/decalage2/ViperMonkey"
license: "BSD"
blackarch-pkgver: "1160.511ecd5"
provides: ["vmonkey"]
---

# Installing vipermonkey

> A VBA parser and emulation engine to analyze malicious macros.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/vipermonkey) (version `1160.511ecd5`, license BSD).

## Arch / BlackArch

```bash
sudo pacman -S vipermonkey    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/decalage2/ViperMonkey) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-pip, python2-virtualenv (legacy; needs python2)

## Installed layout (per recipe)

- Commands: `/usr/bin/vmonkey`

Backlinks: [[forensic]] · [[vipermonkey]] · [[BlackArch Tools]]

