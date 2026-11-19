---
tool: "exabgp"
build-system: "python"
upstream: "https://github.com/Exa-Networks/exabgp"
recipe: "blackarch"
nix-package: "exabgp"
aur-package: "exabgp"
source: "https://github.com/Exa-Networks/exabgp"
license: "custom:unknown"
blackarch-pkgver: "nightly.r0.gd6adb5d"
---

# Installing exabgp

> The BGP swiss army knife of networking.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/exabgp) (version `nightly.r0.gd6adb5d`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S exabgp    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/Exa-Networks/exabgp) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `build`, `ipaddr`, `nose`, `pip`, `psutil`, `wheel`
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
- Data: `/usr/share/man`

Backlinks: [[networking]] · [[exabgp]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#exabgp    # ad-hoc run, no install
nix profile install nixpkgs#exabgp   # or persist it
```
