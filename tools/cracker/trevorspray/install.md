---
tool: "trevorspray"
build-system: "python"
upstream: "https://github.com/blacklanternsecurity/TREVORspray"
recipe: "blackarch"
nix-package: "trevorspray"
source: "https://github.com/blacklanternsecurity/TREVORspray"
license: "GPL3"
blackarch-pkgver: "2.3.0"
---

# Installing trevorspray

> A modular password sprayer with threading, clever proxying, loot modules, and more!

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/trevorspray) (version `2.3.0`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S trevorspray    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/blacklanternsecurity/TREVORspray) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `beautifulsoup4`, `build`, `exchangelib`, `mechanicalsoup`, `pip`, `pygments`, `pysocks`, `tldextract`
- **System libraries** (Arch name — translate for your distro):
  - `trevorproxy`

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  python -m build --wheel --outdir="$startdir/dist"
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[cracker]] · [[trevorspray]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#trevorspray    # ad-hoc run, no install
nix profile install nixpkgs#trevorspray   # or persist it
```
