---
tool: "regrippy"
build-system: "python"
upstream: "https://pypi.org/project/regrippy/#files"
recipe: "blackarch"
nix-package: "regrippy"
source: "https://files.pythonhosted.org/packages/5e/93/3d604be8130408a9b839f8b506732fb2db4b0f4acf6f66082574e2409364/regrippy-2.0.0.tar.gz"
license: "Apache"
blackarch-pkgver: "2.0.0"
provides: ["regrip", "regrip.py"]
---

# Installing regrippy

> Framework for reading and extracting useful forensics data from Windows registry hives.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/regrippy) (version `2.0.0`, license Apache).

## Arch / BlackArch

```bash
sudo pacman -S regrippy    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
curl -fLO https://files.pythonhosted.org/packages/5e/93/3d604be8130408a9b839f8b506732fb2db4b0f4acf6f66082574e2409364/regrippy-2.0.0.tar.gz
tar xf regrippy-2.0.0.tar.gz
cd regrippy-2.0.0
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `python-registry`, `setuptools`, `wheel`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  python setup.py build
```

## Installed layout (per recipe)

- Commands: `/usr/bin/regrip`, `/usr/bin/regrip.py`

Backlinks: [[forensic]] · [[regrippy]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#regrippy    # ad-hoc run, no install
nix profile install nixpkgs#regrippy   # or persist it
```
