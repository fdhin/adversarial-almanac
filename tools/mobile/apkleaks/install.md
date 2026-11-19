---
tool: "apkleaks"
build-system: "python"
upstream: "https://github.com/dwisiswant0/apkleaks"
recipe: "blackarch"
nix-package: "apkleaks"
source: "https://github.com/dwisiswant0/apkleaks"
license: "Apache"
blackarch-pkgver: "v2.6.3.r2.gdd5271a"
---

# Installing apkleaks

> Scanning APK file for URIs, endpoints & secrets.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/apkleaks) (version `v2.6.3.r2.gdd5271a`, license Apache).

## Arch / BlackArch

```bash
sudo pacman -S apkleaks    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/dwisiswant0/apkleaks) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `build`, `pip`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `jadx`
  - `pyaxmlparser`
  - `python`

## Exact build steps from the recipe

```bash
cd $pkgname

  python -m build --wheel --outdir="$startdir/dist"
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[mobile]] · [[apkleaks]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#apkleaks    # ad-hoc run, no install
nix profile install nixpkgs#apkleaks   # or persist it
```
