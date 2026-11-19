---
tool: "detect-secrets"
build-system: "python"
upstream: "https://github.com/Yelp/detect-secrets"
recipe: "blackarch"
nix-package: "detect-secrets"
source: "https://github.com/Yelp/detect-secrets"
license: "Apache"
blackarch-pkgver: "v1.5.0.r50.g5e141933"
---

# Installing detect-secrets

> An enterprise friendly way of detecting and preventing secrets in code.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/detect-secrets) (version `v1.5.0.r50.g5e141933`, license Apache).

## Arch / BlackArch

```bash
sudo pacman -S detect-secrets    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/Yelp/detect-secrets) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `ahocorasick`, `requests`, `setuptools`, `yaml`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `gibberish-detector`
  - `python`

## Exact build steps from the recipe

```bash
cd $pkgname

  python setup.py build
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[code-audit]] · [[detect-secrets]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#detect-secrets    # ad-hoc run, no install
nix profile install nixpkgs#detect-secrets   # or persist it
```
