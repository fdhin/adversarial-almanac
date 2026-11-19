---
tool: "graphinder"
build-system: "python"
upstream: "https://github.com/Escape-Technologies/graphinder"
recipe: "blackarch"
nix-package: "graphinder"
source: "https://github.com/Escape-Technologies/graphinder"
license: "MIT"
blackarch-pkgver: "1.11.6"
---

# Installing graphinder

> GraphQL endpoints finder using subdomain enumeration, scripts analysis and bruteforce.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/graphinder) (version `1.11.6`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S graphinder    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/Escape-Technologies/graphinder) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `aiohttp`, `beautifulsoup4`, `build`, `pip`, `requests`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  python -m build --wheel --outdir="$startdir/dist"
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[graphinder]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#graphinder    # ad-hoc run, no install
nix profile install nixpkgs#graphinder   # or persist it
```
