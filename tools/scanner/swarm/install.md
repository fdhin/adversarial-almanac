---
tool: "swarm"
build-system: "scripts"
upstream: "https://github.com/Arvin-X/swarm"
recipe: "blackarch"
nix-package: "swarm"
source: "https://github.com/Arvin-X/swarm"
license: "GPL3"
blackarch-pkgver: "41.1713c1e"
---

# Installing swarm

> A distributed penetration testing tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/swarm) (version `41.1713c1e`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S swarm    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/Arvin-X/swarm) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-ipy, python2-pymongo (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[swarm]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#swarm    # ad-hoc run, no install
nix profile install nixpkgs#swarm   # or persist it
```
