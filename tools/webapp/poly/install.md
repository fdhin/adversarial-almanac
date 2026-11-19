---
tool: "poly"
build-system: "scripts"
upstream: "https://github.com/grCod/poly"
recipe: "blackarch"
nix-package: "poly"
source: "https://github.com/grCod/poly"
license: "MIT"
blackarch-pkgver: "52.4e6f189"
---

# Installing poly

> Polymorphic webshells.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/poly) (version `52.4e6f189`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S poly    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/grCod/poly) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-argparse (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[poly]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#poly    # ad-hoc run, no install
nix profile install nixpkgs#poly   # or persist it
```
