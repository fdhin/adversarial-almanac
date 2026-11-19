---
tool: "crowbar"
build-system: "python"
upstream: "https://github.com/galkan/crowbar"
recipe: "blackarch"
nix-package: "crowbar"
aur-package: "crowbar"
source: "https://github.com/galkan/crowbar"
license: "MIT"
blackarch-pkgver: "v4.2.r1.g4b563dc"
---

# Installing crowbar

> Brute forcing tool that can be used during penetration tests.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/crowbar) (version `v4.2.r1.g4b563dc`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S crowbar    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/galkan/crowbar) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `paramiko`, `python-nmap`, `setuptools`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `freerdp`
  - `openssh`
  - `openvpn`
  - `python`
  - `tigervnc`

## Exact build steps from the recipe

```bash
cd $pkgname

  python setup.py build
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[cracker]] · [[crowbar]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#crowbar    # ad-hoc run, no install
nix profile install nixpkgs#crowbar   # or persist it
```
