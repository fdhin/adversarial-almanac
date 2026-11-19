---
tool: "ghauri"
build-system: "scripts"
upstream: "https://github.com/r0oth3x49/ghauri"
recipe: "blackarch"
nix-package: "ghauri"
source: "https://github.com/r0oth3x49/ghauri"
license: "MIT"
blackarch-pkgver: "1.4.3.r0.g18e3677"
---

# Installing ghauri

> An advanced cross-platform tool that automates the process of detecting and exploiting SQL injection security flaws.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/ghauri) (version `1.4.3.r0.g18e3677`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S ghauri    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/r0oth3x49/ghauri) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `pip`, `setuptools`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[ghauri]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#ghauri    # ad-hoc run, no install
nix profile install nixpkgs#ghauri   # or persist it
```
