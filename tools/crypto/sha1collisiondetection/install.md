---
tool: "sha1collisiondetection"
build-system: "scripts"
upstream: "https://github.com/cr-marcstevens/sha1collisiondetection"
recipe: "blackarch"
nix-package: "sha1collisiondetection"
source: "https://github.com/cr-marcstevens/sha1collisiondetection"
license: "MIT"
blackarch-pkgver: "106.a06f1ad"
---

# Installing sha1collisiondetection

> Library and command line tool to detect SHA collision in a file

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/sha1collisiondetection) (version `106.a06f1ad`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S sha1collisiondetection    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/cr-marcstevens/sha1collisiondetection) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[crypto]] · [[sha1collisiondetection]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#sha1collisiondetection    # ad-hoc run, no install
nix profile install nixpkgs#sha1collisiondetection   # or persist it
```
