---
tool: "trevorproxy"
build-system: "python"
upstream: "https://github.com/blacklanternsecurity/TREVORproxy"
recipe: "blackarch"
nix-package: "trevorproxy"
source: "https://github.com/blacklanternsecurity/TREVORproxy"
license: "GPL3"
blackarch-pkgver: "1.0.6"
---

# Installing trevorproxy

> A SOCKS proxy written in Python that randomizes your source IP address.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/trevorproxy) (version `1.0.6`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S trevorproxy    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/blacklanternsecurity/TREVORproxy) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `build`, `pip`, `sh`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  python -m build --wheel --outdir="$startdir/dist"
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[proxy]] · [[trevorproxy]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#trevorproxy    # ad-hoc run, no install
nix profile install nixpkgs#trevorproxy   # or persist it
```
