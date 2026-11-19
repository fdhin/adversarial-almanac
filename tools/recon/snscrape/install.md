---
tool: "snscrape"
build-system: "python"
upstream: "https://github.com/JustAnotherArchivist/snscrape"
recipe: "blackarch"
nix-package: "snscrape"
source: "https://github.com/JustAnotherArchivist/snscrape"
license: "GPL3"
blackarch-pkgver: "0.4.3.20220106"
---

# Installing snscrape

> A social networking service scraper in Python.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/snscrape) (version `0.4.3.20220106`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S snscrape    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/JustAnotherArchivist/snscrape) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `beautifulsoup4`, `filelock`, `lxml`, `pip`, `requests`, `setuptools`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  python setup.py build
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[snscrape]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#snscrape    # ad-hoc run, no install
nix profile install nixpkgs#snscrape   # or persist it
```
