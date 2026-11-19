---
tool: "detect-it-easy"
build-system: "scripts"
upstream: "https://github.com/horsicq/DIE-engine/releases"
recipe: "blackarch"
nix-package: "detect-it-easy"
source: "https://github.com/horsicq/DIE-engine/releases"
license: "MIT"
blackarch-pkgver: "3.10"
---

# Installing detect-it-easy

> A program for determining types of files.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/detect-it-easy) (version `3.10`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S detect-it-easy    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/horsicq/DIE-engine/releases) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `qt5-script`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[binary]] · [[detect-it-easy]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#detect-it-easy    # ad-hoc run, no install
nix profile install nixpkgs#detect-it-easy   # or persist it
```
