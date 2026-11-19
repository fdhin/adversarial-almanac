---
tool: "evillimiter"
build-system: "python"
upstream: "https://github.com/bitbrute/evillimiter"
recipe: "blackarch"
nix-package: "evillimiter"
source: "https://github.com/bitbrute/evillimiter"
license: "MIT"
blackarch-pkgver: "36.46d2033"
---

# Installing evillimiter

> Tool that limits bandwidth of devices on the same network without access.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/evillimiter) (version `36.46d2033`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S evillimiter    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/bitbrute/evillimiter) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `colorama`, `netaddr`, `netifaces`, `setuptools`, `terminaltables`, `tqdm`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`
  - `scapy`

## Exact build steps from the recipe

```bash
cd $pkgname

  python setup.py build
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[networking]] · [[evillimiter]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#evillimiter    # ad-hoc run, no install
nix profile install nixpkgs#evillimiter   # or persist it
```
