---
tool: "cribdrag"
build-system: "scripts"
upstream: "https://github.com/SpiderLabs/cribdrag"
recipe: "blackarch"
source: "https://github.com/SpiderLabs/cribdrag"
license: "GPL3"
blackarch-pkgver: "5.2d27dbf"
provides: ["cribdrag-xorstrings"]
---

# Installing cribdrag

> An interactive crib dragging tool for cryptanalysis on ciphertext generated with reused or predictable stream cipher keys.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/cribdrag) (version `5.2d27dbf`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S cribdrag    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/SpiderLabs/cribdrag) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Installed layout (per recipe)

- Commands: `/usr/bin/cribdrag-xorstrings`

Backlinks: [[crypto]] · [[cribdrag]] · [[BlackArch Tools]]

