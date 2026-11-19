---
tool: "sigma"
build-system: "python"
upstream: "https://github.com/SigmaHQ/sigma/releases"
recipe: "blackarch"
aur-package: "sigma"
source: "https://github.com/SigmaHQ/sigma/releases"
license: "LGPL3"
blackarch-pkgver: "0.20"
---

# Installing sigma

> Generic Signature Format for SIEM Systems

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/sigma) (version `0.20`, license LGPL3).

## Arch / BlackArch

```bash
sudo pacman -S sigma    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/SigmaHQ/sigma/releases) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `progressbar`, `pymisp`, `requests`, `ruamel-yaml`, `setuptools`, `urllib3`, `yaml`

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver/tools"

  python setup.py build
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[defensive]] · [[sigma]] · [[BlackArch Tools]]

