---
tool: "arjun"
build-system: "python"
upstream: "https://github.com/s0md3v/Arjun"
recipe: "blackarch"
nix-package: "arjun"
aur-package: "arjun"
source: "https://github.com/s0md3v/Arjun"
license: "GPL3"
blackarch-pkgver: "2.2.7.r5.gd1fb995"
---

# Installing arjun

> HTTP parameter discovery suite.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/arjun) (version `2.2.7.r5.gd1fb995`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S arjun    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/s0md3v/Arjun.git
cd Arjun
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `dicttoxml`, `ratelimit`, `requests`, `setuptools`
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

Backlinks: [[scanner]] · [[arjun]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#arjun    # ad-hoc run, no install
nix profile install nixpkgs#arjun   # or persist it
```
