---
tool: "python-api-dnsdumpster"
build-system: "python"
upstream: "https://github.com/PaulSec/API-dnsdumpster.com"
recipe: "blackarch"
source: "https://github.com/PaulSec/API-dnsdumpster.com"
license: "custom:unknown"
blackarch-pkgver: "v0.11.r0.g7c60722"
---

# Installing python-api-dnsdumpster

> Unofficial Python API for http://dnsdumpster.com/.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/python-api-dnsdumpster) (version `v0.11.r0.g7c60722`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S python-api-dnsdumpster    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/PaulSec/API-dnsdumpster.com.git
cd API-dnsdumpster.com
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `beautifulsoup4`, `requests`, `setuptools`
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

Backlinks: [[recon]] · [[python-api-dnsdumpster]] · [[BlackArch Tools]]

