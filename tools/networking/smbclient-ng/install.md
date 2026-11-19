---
tool: "smbclient-ng"
build-system: "python"
upstream: "https://github.com/p0dalirius/smbclient-ng"
recipe: "blackarch"
nix-package: "smbclient-ng"
source: "https://github.com/p0dalirius/smbclient-ng"
license: "GPL-3.0-only"
blackarch-pkgver: "3.1.0.r4.g755200b"
---

# Installing smbclient-ng

> Interact with SMB shares.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/smbclient-ng) (version `3.1.0.r4.g755200b`, license GPL-3.0-only).

## Arch / BlackArch

```bash
sudo pacman -S smbclient-ng    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/p0dalirius/smbclient-ng) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `build`, `charset-normalizer`, `pefile`, `pip`, `rich`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `impacket-ba`
  - `python`

## Exact build steps from the recipe

```bash
cd $pkgname

  python -m build --wheel --outdir="$startdir/dist"
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[networking]] · [[smbclient-ng]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#smbclient-ng    # ad-hoc run, no install
nix profile install nixpkgs#smbclient-ng   # or persist it
```
