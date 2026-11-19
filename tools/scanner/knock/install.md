---
tool: "knock"
build-system: "python"
upstream: "https://github.com/guelfoweb/knock"
recipe: "blackarch"
nix-package: "knock"
source: "https://github.com/guelfoweb/knock"
license: "GPL3"
blackarch-pkgver: "116.da43fdd"
provides: ["knockpy"]
---

# Installing knock

> Subdomain scanner.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/knock) (version `116.da43fdd`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S knock    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/guelfoweb/knock) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `beautifulsoup4`, `colorama`, `dnspython`, `matplotlib`, `networkx`, `requests`, `setuptools`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Exact build steps from the recipe

```bash
cd $pkgname

  python setup.py build
```

## Installed layout (per recipe)

- Commands: `/usr/bin/knockpy`

Backlinks: [[scanner]] · [[knock]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#knock    # ad-hoc run, no install
nix profile install nixpkgs#knock   # or persist it
```
