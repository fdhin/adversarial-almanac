---
tool: "reptor"
build-system: "python"
upstream: "https://github.com/Syslifters/reptor"
recipe: "blackarch"
nix-package: "reptor"
source: "https://github.com/Syslifters/reptor"
license: "custom:MIT"
blackarch-pkgver: "0.7"
---

# Installing reptor

> CLI tool to automate pentest reporting with SysReptor.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/reptor) (version `0.7`, license custom:MIT).

## Arch / BlackArch

```bash
sudo pacman -S reptor    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/Syslifters/reptor) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `asgiref`, `build`, `certifi`, `charset-normalizer`, `cvss`, `django`, `idna`, `markdown-it-py`, `mdurl`, `pip`, `pyaml`, `pygments`, `requests`, `rich`, `sqlparse`, `termcolor`, `tomli`, `tomli-w`, `urllib3`, `xmltodict`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  python -m build --wheel --outdir="$startdir/dist"
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[misc]] · [[reptor]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#reptor    # ad-hoc run, no install
nix profile install nixpkgs#reptor   # or persist it
```
