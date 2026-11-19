---
tool: "unblob"
build-system: "python"
upstream: "https://github.com/onekey-sec/unblob"
recipe: "blackarch"
nix-package: "unblob"
source: "https://github.com/onekey-sec/unblob"
license: "MIT"
blackarch-pkgver: "26.6.4.r300.g9f12939"
---

# Installing unblob

> Extract files from any kind of container formats.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/unblob) (version `26.6.4.r300.g9f12939`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S unblob    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/onekey-sec/unblob) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `arpy`, `attrs`, `build`, `click`, `cryptography`, `dissect.cstruct`, `lark`, `lief`, `lz4`, `magic`, `pip`, `plotext`, `pluggy`, `pydantic`, `pyfatfs`, `pymdown-extensions`, `pyperscan`, `pyzstd`, `rarfile`, `rich`, `structlog`, `treelib`, `ubi-reader`, `unblob-native`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `jefferson`
  - `python`

## Exact build steps from the recipe

```bash
cd $pkgname

  python -m build --wheel --outdir="$startdir/dist"
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[forensic]] · [[unblob]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#unblob    # ad-hoc run, no install
nix profile install nixpkgs#unblob   # or persist it
```
