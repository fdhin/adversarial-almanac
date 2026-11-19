---
tool: "fierce"
build-system: "python"
upstream: "https://github.com/mschwager/fierce"
recipe: "blackarch"
nix-package: "fierce"
source: "https://github.com/mschwager/fierce"
license: "GPL3"
blackarch-pkgver: "145.965840d"
kali-package: "fierce"
kali-version: "1.6.0-2"
---

# Installing fierce

> A DNS reconnaissance tool for locating non-contiguous IP space.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/fierce) (version `145.965840d`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S fierce    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install fierce    # Kali package version 1.6.0-2
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/mschwager/fierce) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `build`, `dnspython`, `pip`
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

Backlinks: [[recon]] · [[fierce]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#fierce    # ad-hoc run, no install
nix profile install nixpkgs#fierce   # or persist it
```
