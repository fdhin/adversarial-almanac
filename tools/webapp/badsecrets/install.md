---
tool: "badsecrets"
build-system: "python"
upstream: "https://github.com/blacklanternsecurity/badsecrets"
recipe: "blackarch"
nix-package: "badsecrets"
source: "https://github.com/blacklanternsecurity/badsecrets"
license: "GPL-3.0-or-later"
blackarch-pkgver: "1.3.0.r4.g66e88a0"
---

# Installing badsecrets

> A library for detecting known secrets across many web frameworks.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/badsecrets) (version `1.3.0.r4.g66e88a0`, license GPL-3.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S badsecrets    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/blacklanternsecurity/badsecrets) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `build`, `django`, `pip`, `pycryptodome`, `pyjwt`, `viewstate`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `flask-unsign`
  - `python`

## Exact build steps from the recipe

```bash
cd $pkgname

  python -m build --wheel --outdir="$startdir/dist"
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[badsecrets]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#badsecrets    # ad-hoc run, no install
nix profile install nixpkgs#badsecrets   # or persist it
```
