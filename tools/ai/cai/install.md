---
tool: "cai"
build-system: "scripts"
upstream: "https://github.com/aliasrobotics/cai"
recipe: "blackarch"
nix-package: "cai"
aur-package: "cai"
source: "https://github.com/aliasrobotics/cai"
license: "MIT"
blackarch-pkgver: "1.6dc7925"
---

# Installing cai

> The framework for AI Security.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/cai) (version `1.6dc7925`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S cai    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/aliasrobotics/cai) (the recipe uses a versioned archive).

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

Backlinks: [[ai]] · [[cai]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#cai    # ad-hoc run, no install
nix profile install nixpkgs#cai   # or persist it
```
