---
tool: "crackql"
build-system: "scripts"
upstream: "https://github.com/nicholasaleks/CrackQL"
recipe: "blackarch"
nix-package: "crackql"
source: "https://github.com/nicholasaleks/CrackQL"
license: "BSD"
blackarch-pkgver: "1.0.r53.gac26a44"
---

# Installing crackql

> GraphQL password brute-force and fuzzing utility

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/crackql) (version `1.0.r53.gac26a44`, license BSD).

## Arch / BlackArch

```bash
sudo pacman -S crackql    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/nicholasaleks/CrackQL) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `pip`, `virtualenv`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[crackql]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#crackql    # ad-hoc run, no install
nix profile install nixpkgs#crackql   # or persist it
```
