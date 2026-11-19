---
tool: "python-frida-tools"
build-system: "python"
upstream: "https://pypi.org/project/frida-tools/#files"
recipe: "blackarch"
source: "https://pypi.org/project/frida-tools/#files"
license: "custom:unknown"
blackarch-pkgver: "14.11.0"
---

# Installing python-frida-tools

> Frida CLI tools.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/python-frida-tools) (version `14.11.0`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S python-frida-tools    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://pypi.org/project/frida-tools/#files) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `colorama`, `frida`, `prompt_toolkit`, `pygments`, `setuptools`, `websockets`
- **System libraries** (Arch name — translate for your distro):
  - `libimobiledevice`
  - `python`

## Exact build steps from the recipe

```bash
cd "$_pkgname-$pkgver"

  python setup.py build
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[mobile]] · [[python-frida-tools]] · [[BlackArch Tools]]

