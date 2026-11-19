---
tool: "enum4linux-ng"
build-system: "python"
upstream: "https://github.com/cddmp/enum4linux-ng"
recipe: "blackarch"
nix-package: "enum4linux-ng"
aur-package: "enum4linux-ng"
source: "https://github.com/cddmp/enum4linux-ng"
license: "GPL3"
blackarch-pkgver: "454.288826f"
---

# Installing enum4linux-ng

> A next generation version of enum4linux.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/enum4linux-ng) (version `454.288826f`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S enum4linux-ng    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/cddmp/enum4linux-ng) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `ldap3`, `setuptools`, `yaml`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `impacket`
  - `python`
  - `smbclient`

## Exact build steps from the recipe

```bash
cd $pkgname

  python setup.py build
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[enum4linux-ng]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#enum4linux-ng    # ad-hoc run, no install
nix profile install nixpkgs#enum4linux-ng   # or persist it
```
