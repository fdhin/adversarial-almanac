---
tool: "h8mail"
build-system: "python"
upstream: "https://github.com/khast3x/h8mail"
recipe: "blackarch"
nix-package: "h8mail"
source: "https://github.com/khast3x/h8mail"
license: "custom:unknown"
blackarch-pkgver: "344.ee31c8f"
---

# Installing h8mail

> Email OSINT and password breach hunting.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/h8mail) (version `344.ee31c8f`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S h8mail    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/khast3x/h8mail) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `requests`, `setuptools`
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

Backlinks: [[recon]] · [[h8mail]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#h8mail    # ad-hoc run, no install
nix profile install nixpkgs#h8mail   # or persist it
```
