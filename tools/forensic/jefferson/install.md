---
tool: "jefferson"
build-system: "python"
upstream: "https://github.com/onekey-sec/jefferson"
recipe: "blackarch"
nix-package: "jefferson"
aur-package: "jefferson"
source: "https://github.com/onekey-sec/jefferson"
license: "MIT"
blackarch-pkgver: "v0.4.8.r21.gf605cd1"
---

# Installing jefferson

> JFFS2 filesystem extraction tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/jefferson) (version `v0.4.8.r21.gf605cd1`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S jefferson    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/onekey-sec/jefferson) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `build`, `click`, `cstruct`, `lzallright`, `pip`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Exact build steps from the recipe

```bash
cd $pkgname

  python -m build --wheel --outdir="$startdir/dist"
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[forensic]] · [[jefferson]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#jefferson    # ad-hoc run, no install
nix profile install nixpkgs#jefferson   # or persist it
```
