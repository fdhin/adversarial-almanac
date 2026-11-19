---
tool: "androguard"
build-system: "python"
upstream: "https://github.com/androguard/androguard"
recipe: "blackarch"
nix-package: "androguard"
aur-package: "androguard"
source: "https://github.com/androguard/androguard"
license: "APACHE"
blackarch-pkgver: "3.3.5"
---

# Installing androguard

> Reverse engineering, Malware and goodware analysis of Android applications and more.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/androguard) (version `3.3.5`, license APACHE).

## Arch / BlackArch

```bash
sudo pacman -S androguard    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/androguard/androguard) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `asn1crypto`, `click`, `colorama`, `future`, `lxml`, `matplotlib`, `networkx`, `pydot`, `pygments`, `setuptools`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `ipython`
  - `python`

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  python setup.py build
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[binary]] · [[androguard]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#androguard    # ad-hoc run, no install
nix profile install nixpkgs#androguard   # or persist it
```
