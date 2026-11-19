---
tool: "socialscan"
build-system: "python"
upstream: "https://github.com/iojw/socialscan"
recipe: "blackarch"
nix-package: "socialscan"
source: "https://github.com/iojw/socialscan"
license: "MIT"
blackarch-pkgver: "128.5ae42d0"
---

# Installing socialscan

> Check email address and username availability on online platforms.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/socialscan) (version `128.5ae42d0`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S socialscan    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/iojw/socialscan) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `aiohttp`, `colorama`, `setuptools`, `tqdm`
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

Backlinks: [[recon]] · [[socialscan]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#socialscan    # ad-hoc run, no install
nix profile install nixpkgs#socialscan   # or persist it
```
