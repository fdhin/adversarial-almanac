---
tool: "pybozocrack"
build-system: "python"
upstream: "https://github.com/ikkebr/PyBozoCrack"
recipe: "blackarch"
source: "https://github.com/ikkebr/PyBozoCrack"
license: "custom:unknown"
blackarch-pkgver: "87.ceb0cd9"
---

# Installing pybozocrack

> A silly & effective MD5 cracker in Python.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/pybozocrack) (version `87.ceb0cd9`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S pybozocrack    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/ikkebr/PyBozoCrack.git
cd PyBozoCrack
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `requests`, `setuptools`, `wheel`
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

Backlinks: [[cracker]] · [[pybozocrack]] · [[BlackArch Tools]]

