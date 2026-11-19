---
tool: "rawsec-cli"
build-system: "python"
upstream: "https://github.com/mBouamama/rawsec_cli"
recipe: "blackarch"
source: "https://github.com/mBouamama/rawsec_cli"
license: "MIT"
blackarch-pkgver: "1.2.0.r7.gf7a08c6"
---

# Installing rawsec-cli

> Rawsec Inventory search CLI to find security tools and resources.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/rawsec-cli) (version `1.2.0.r7.gf7a08c6`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S rawsec-cli    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/mBouamama/rawsec_cli) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `click`, `columnar`, `gitpython`, `requests`, `setuptools`, `tabulate`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Exact build steps from the recipe

```bash
cd $_pkgname

  python setup.py build
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[misc]] · [[rawsec-cli]] · [[BlackArch Tools]]

