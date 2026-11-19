---
tool: "chipsec"
build-system: "python"
upstream: "https://github.com/chipsec/chipsec"
recipe: "blackarch"
nix-package: "chipsec"
source: "https://github.com/chipsec/chipsec"
license: "GPL2"
blackarch-pkgver: "2.0.9.r11.g2545706"
---

# Installing chipsec

> Platform Security Assessment Framework.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/chipsec) (version `2.0.9.r11.g2545706`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S chipsec    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/chipsec/chipsec) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `lxml`, `setuptools`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `dkms`
  - `libelf`
  - `linux-headers`
  - `nasm`
  - `python`

## Exact build steps from the recipe

```bash
cd $pkgname

  python setup.py build --skip-driver
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[hardware]] · [[chipsec]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#chipsec    # ad-hoc run, no install
nix profile install nixpkgs#chipsec   # or persist it
```
