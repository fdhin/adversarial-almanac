---
tool: "amoco"
build-system: "python"
upstream: "https://github.com/bdcht/amoco"
recipe: "blackarch"
nix-package: "amoco"
source: "https://github.com/bdcht/amoco"
license: "GPL2"
blackarch-pkgver: "v2.9.11.r4.g3e36c52"
---

# Installing amoco

> Yet another tool for analysing binaries.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/amoco) (version `v2.9.11.r4.g3e36c52`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S amoco    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/bdcht/amoco) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `blessed`, `click`, `crysp`, `grandalf`, `pip`, `pyparsing`, `pyqt5`, `pytest`, `setuptools`, `tqdm`, `traitlets`, `z3-solver`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `pygoocanvas`
  - `pygtk`
  - `python`

## Exact build steps from the recipe

```bash
cd $pkgname

  python setup.py build
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[binary]] · [[amoco]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#amoco    # ad-hoc run, no install
nix profile install nixpkgs#amoco   # or persist it
```
