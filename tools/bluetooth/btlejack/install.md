---
tool: "btlejack"
build-system: "python"
upstream: "https://github.com/virtualabs/btlejack"
recipe: "blackarch"
nix-package: "btlejack"
source: "https://github.com/virtualabs/btlejack"
license: "MIT"
blackarch-pkgver: "v2.1.1.r1.gc487859"
---

# Installing btlejack

> Bluetooth Low Energy Swiss-army knife.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/btlejack) (version `v2.1.1.r1.gc487859`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S btlejack    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/virtualabs/btlejack) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `halo`, `pyserial`, `setuptools`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Exact build steps from the recipe

```bash
cd $pkgname

  python setup.py build
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[bluetooth]] · [[btlejack]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#btlejack    # ad-hoc run, no install
nix profile install nixpkgs#btlejack   # or persist it
```
