---
tool: "stacs"
build-system: "python"
upstream: "https://github.com/stacscan/stacs"
recipe: "blackarch"
nix-package: "stacs"
source: "https://github.com/stacscan/stacs"
license: "BSD-3-Clause"
blackarch-pkgver: "0.5.1.r0.g11f3503"
---

# Installing stacs

> Static Token And Credential Scanner.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/stacs) (version `0.5.1.r0.g11f3503`, license BSD-3-Clause).

## Arch / BlackArch

```bash
sudo pacman -S stacs    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/stacscan/stacs) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `click`, `libarchive`, `pip`, `pydantic`, `setuptools`, `yara-python-dex`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `pybind11`
  - `python`

## Exact build steps from the recipe

```bash
cd $pkgname

  python setup.py build
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[stacs]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#stacs    # ad-hoc run, no install
nix profile install nixpkgs#stacs   # or persist it
```
