---
tool: "sherlock"
build-system: "python"
upstream: "https://github.com/sherlock-project/sherlock"
recipe: "blackarch"
nix-package: "sherlock"
aur-package: "sherlock"
source: "https://github.com/sherlock-project/sherlock"
license: "MIT"
blackarch-pkgver: "0.16.0"
pypi: "sherlock-project"
---

# Installing sherlock

> Find usernames across social networks.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/sherlock) (version `0.16.0`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S sherlock    # needs the [blackarch] repo enabled
```

## Any Linux distro

**A. From PyPI (any distro with Python 3.9+):**

```bash
pipx install sherlock-project
```

(`python3 -m pip install --user sherlock-project` works too)
**B. From source:**

**Source:** download the release from the [upstream site](https://github.com/sherlock-project/sherlock) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `build`, `certifi`, `colorama`, `openpyxl`, `pandas`, `pip`, `pysocks`, `requests`, `requests-futures`, `stem`, `torrequest`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Exact build steps from the recipe

```bash
cd "$_pypi_archive-$pkgver"

  python -m build --wheel --outdir="$startdir/dist"
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[social]] · [[sherlock]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#sherlock    # ad-hoc run, no install
nix profile install nixpkgs#sherlock   # or persist it
```
