---
tool: "gibberish-detector"
build-system: "python"
upstream: "https://github.com/domanchi/gibberish-detector"
recipe: "blackarch"
nix-package: "gibberish-detector"
source: "https://github.com/domanchi/gibberish-detector"
license: "MIT"
blackarch-pkgver: "v0.1.1.r2.gecac969"
---

# Installing gibberish-detector

> Train a model and detect gibberish strings with it.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/gibberish-detector) (version `v0.1.1.r2.gecac969`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S gibberish-detector    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/domanchi/gibberish-detector) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `setuptools`
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

Backlinks: [[misc]] · [[gibberish-detector]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#gibberish-detector    # ad-hoc run, no install
nix profile install nixpkgs#gibberish-detector   # or persist it
```
