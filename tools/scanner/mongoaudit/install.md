---
tool: "mongoaudit"
build-system: "python"
upstream: "https://github.com/stampery/mongoaudit"
recipe: "blackarch"
nix-package: "mongoaudit"
aur-package: "mongoaudit"
source: "https://github.com/stampery/mongoaudit"
license: "MIT"
blackarch-pkgver: "222.70b83e8"
---

# Installing mongoaudit

> A powerful MongoDB auditing and pentesting tool .

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/mongoaudit) (version `222.70b83e8`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S mongoaudit    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/stampery/mongoaudit) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `dnspython`, `pymongo`, `setuptools`, `urwid`
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

Backlinks: [[scanner]] · [[mongoaudit]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#mongoaudit    # ad-hoc run, no install
nix profile install nixpkgs#mongoaudit   # or persist it
```
