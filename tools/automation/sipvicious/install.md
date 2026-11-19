---
tool: "sipvicious"
build-system: "python"
upstream: "http://blog.sipvicious.org/"
recipe: "blackarch"
nix-package: "sipvicious"
aur-package: "sipvicious"
source: "http://blog.sipvicious.org/"
license: "GPL2"
blackarch-pkgver: "481.485d16c"
kali-package: "sipvicious"
kali-version: "0.3.3-2.1"
---

# Installing sipvicious

> Tools for auditing SIP devices.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/sipvicious) (version `481.485d16c`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S sipvicious    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install sipvicious    # Kali package version 0.3.3-2.1
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://blog.sipvicious.org/) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `setuptools`
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

Backlinks: [[automation]] · [[sipvicious]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#sipvicious    # ad-hoc run, no install
nix profile install nixpkgs#sipvicious   # or persist it
```
