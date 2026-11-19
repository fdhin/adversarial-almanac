---
tool: "clairvoyance"
build-system: "python"
upstream: "https://github.com/nikitastupin/clairvoyance"
recipe: "blackarch"
nix-package: "clairvoyance"
source: "https://github.com/nikitastupin/clairvoyance"
license: "MIT"
blackarch-pkgver: "2.5.2"
---

# Installing clairvoyance

> Obtain GraphQL API Schema even if the introspection is not enabled.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/clairvoyance) (version `2.5.2`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S clairvoyance    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/nikitastupin/clairvoyance) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `aiohttp`, `asyncio`, `build`, `pip`, `rich`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  python -m build --wheel --outdir="$startdir/dist"
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[clairvoyance]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#clairvoyance    # ad-hoc run, no install
nix profile install nixpkgs#clairvoyance   # or persist it
```
