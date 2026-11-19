---
tool: "smbmap"
build-system: "python"
upstream: "https://github.com/ShawnDEvans/smbmap"
recipe: "blackarch"
nix-package: "smbmap"
aur-package: "smbmap"
source: "https://github.com/ShawnDEvans/smbmap"
license: "GPL3"
blackarch-pkgver: "v1.10.8.r0.g31b8a8f"
kali-package: "smbmap"
kali-version: "1.10.7-1"
---

# Installing smbmap

> A handy SMB enumeration tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/smbmap) (version `v1.10.8.r0.g31b8a8f`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S smbmap    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install smbmap    # Kali package version 1.10.7-1
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/ShawnDEvans/smbmap) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `build`, `configupdater`, `pip`, `pyasn1`, `pycryptodomex`, `termcolor`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `impacket`
  - `python`

## Exact build steps from the recipe

```bash
cd $pkgname

  python -m build --wheel --outdir="$startdir/dist"
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[smbmap]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#smbmap    # ad-hoc run, no install
nix profile install nixpkgs#smbmap   # or persist it
```
