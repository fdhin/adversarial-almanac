---
tool: "usbrip"
build-system: "python"
upstream: "https://github.com/snovvcrash/usbrip"
recipe: "blackarch"
nix-package: "usbrip"
source: "https://github.com/snovvcrash/usbrip"
license: "GPL3"
blackarch-pkgver: "291.5093c84"
---

# Installing usbrip

> USB device artifacts tracker.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/usbrip) (version `291.5093c84`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S usbrip    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/snovvcrash/usbrip) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `pip`, `setuptools`, `termcolor`, `terminaltables`, `tqdm`, `wheel`
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

Backlinks: [[forensic]] · [[usbrip]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#usbrip    # ad-hoc run, no install
nix profile install nixpkgs#usbrip   # or persist it
```
