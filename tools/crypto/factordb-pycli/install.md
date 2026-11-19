---
tool: "factordb-pycli"
build-system: "python"
upstream: "https://github.com/ryosan-470/factordb-pycli"
recipe: "blackarch"
source: "https://github.com/ryosan-470/factordb-pycli"
license: "MIT"
blackarch-pkgver: "1.3.0"
---

# Installing factordb-pycli

> CLI for factordb and Python API Client.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/factordb-pycli) (version `1.3.0`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S factordb-pycli    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/ryosan/factordb-pycli) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `pip`, `requests`, `setuptools`, `wheel`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Exact build steps from the recipe

```bash
cd "$_pkgname-$pkgver"

  python setup.py build
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[crypto]] · [[factordb-pycli]] · [[BlackArch Tools]]

