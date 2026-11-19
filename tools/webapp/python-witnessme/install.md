---
tool: "python-witnessme"
build-system: "python"
upstream: "https://pypi.org/project/witnessme/#files"
recipe: "blackarch"
source: "https://pypi.org/project/witnessme/#files"
license: "GPL3"
blackarch-pkgver: "1.5.0"
kali-package: "witnessme"
kali-version: "1.5.0+git20201026-0kali5"
---

# Installing python-witnessme

> Web Inventory tool, takes screenshots of webpages using Pyppeteer.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/python-witnessme) (version `1.5.0`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S python-witnessme    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install witnessme    # Kali package version 1.5.0+git20201026-0kali5
```

(Debian/Kali name the package `witnessme`, not `python-witnessme`.)

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://pypi.org/project/witnessme/#files) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `aiodns`, `aiosqlite`, `appdirs`, `imgcat`, `lxml`, `prompt_toolkit`, `pyee`, `pyppeteer`, `setuptools`, `six`, `terminaltables`, `tqdm`, `urllib3`, `wcwidth`, `websockets`, `xmltodict`, `yaml`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Exact build steps from the recipe

```bash
cd "$_pkgname-$pkgver"

  python setup.py build
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[python-witnessme]] · [[BlackArch Tools]]

