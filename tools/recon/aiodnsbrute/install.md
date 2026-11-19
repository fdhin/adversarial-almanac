---
tool: "aiodnsbrute"
build-system: "python"
upstream: "https://github.com/blark/aiodnsbrute"
recipe: "blackarch"
nix-package: "aiodnsbrute"
source: "https://github.com/blark/aiodnsbrute"
license: "GPL-3.0-or-later"
blackarch-pkgver: "v0.3.2.r0.ge773a4c"
---

# Installing aiodnsbrute

> Python 3 DNS asynchronous brute force utility.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/aiodnsbrute) (version `v0.3.2.r0.ge773a4c`, license GPL-3.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S aiodnsbrute    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/blark/aiodnsbrute) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `aiodns`, `asyncio`, `click`, `setuptools`, `tqdm`, `uvloop`
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

Backlinks: [[recon]] · [[aiodnsbrute]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#aiodnsbrute    # ad-hoc run, no install
nix profile install nixpkgs#aiodnsbrute   # or persist it
```
