---
tool: "stoq"
build-system: "python"
upstream: "https://github.com/PUNCH-Cyber/stoq"
recipe: "blackarch"
source: "https://github.com/PUNCH-Cyber/stoq"
license: "Apache"
blackarch-pkgver: "769.8bfc78b"
---

# Installing stoq

> An open source framework for enterprise level automated analysis.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/stoq) (version `769.8bfc78b`, license Apache).

## Arch / BlackArch

```bash
sudo pacman -S stoq    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/PUNCH-Cyber/stoq) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `beautifulsoup4`, `json-logger`, `requests`, `setuptools`
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

Backlinks: [[code-audit]] · [[stoq]] · [[BlackArch Tools]]

