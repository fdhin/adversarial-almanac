---
tool: "snallygaster"
build-system: "python"
upstream: "https://github.com/hannob/snallygaster"
recipe: "blackarch"
nix-package: "snallygaster"
aur-package: "snallygaster"
source: "https://github.com/hannob/snallygaster"
license: "custom:unknown"
blackarch-pkgver: "v0.0.15.r5.g1819e27"
---

# Installing snallygaster

> Tool to scan for secret files on HTTP servers.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/snallygaster) (version `v0.0.15.r5.g1819e27`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S snallygaster    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/hannob/snallygaster) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `beautifulsoup4`, `build`, `dnspython`, `pip`, `urllib3`
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

Backlinks: [[webapp]] · [[snallygaster]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#snallygaster    # ad-hoc run, no install
nix profile install nixpkgs#snallygaster   # or persist it
```
