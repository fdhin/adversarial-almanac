---
tool: "mitm6"
build-system: "python"
upstream: "https://github.com/fox-it/mitm6"
recipe: "blackarch"
nix-package: "mitm6"
source: "https://github.com/fox-it/mitm6"
license: "GPL2"
blackarch-pkgver: "33.8e75884"
---

# Installing mitm6

> Pwning IPv4 via IPv6.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/mitm6) (version `33.8e75884`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S mitm6    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/fox-it/mitm6) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `netifaces`, `setuptools`, `twisted`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`
  - `scapy`

## Exact build steps from the recipe

```bash
cd $pkgname

  python setup.py build
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[mitm6]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#mitm6    # ad-hoc run, no install
nix profile install nixpkgs#mitm6   # or persist it
```
