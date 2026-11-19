---
tool: "holehe"
build-system: "python"
upstream: "https://github.com/megadose/holehe"
recipe: "blackarch"
nix-package: "holehe"
aur-package: "holehe"
source: "https://github.com/megadose/holehe"
license: "GPL3"
blackarch-pkgver: "434.14da70f"
---

# Installing holehe

> A tool for Efficiently finding registered accounts from emails.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/holehe) (version `434.14da70f`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S holehe    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/megadose/holehe) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `beautifulsoup4`, `colorama`, `httpx`, `setuptools`, `termcolor`, `tqdm`, `trio`
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

Backlinks: [[social]] · [[holehe]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#holehe    # ad-hoc run, no install
nix profile install nixpkgs#holehe   # or persist it
```
