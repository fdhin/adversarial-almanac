---
tool: "uefi-firmware-parser"
build-system: "python"
upstream: "https://github.com/theopolis/uefi-firmware-parser"
recipe: "blackarch"
nix-package: "uefi-firmware-parser"
source: "https://github.com/theopolis/uefi-firmware-parser"
license: "GPL3"
blackarch-pkgver: "215.c4b8c64"
---

# Installing uefi-firmware-parser

> Parse BIOS/Intel ME/UEFI firmware related structures: Volumes, FileSystems, Files, etc.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/uefi-firmware-parser) (version `215.c4b8c64`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S uefi-firmware-parser    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/theopolis/uefi-firmware-parser) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `pefile`, `setuptools`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Exact build steps from the recipe

```bash
cd $pkgname

  python setup.py build
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[firmware]] · [[uefi-firmware-parser]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#uefi-firmware-parser    # ad-hoc run, no install
nix profile install nixpkgs#uefi-firmware-parser   # or persist it
```
