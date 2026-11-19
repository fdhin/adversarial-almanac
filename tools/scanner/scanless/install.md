---
tool: "scanless"
build-system: "python"
upstream: "https://github.com/vesche/scanless"
recipe: "blackarch"
source: "https://github.com/vesche/scanless"
license: "custom:unknown"
blackarch-pkgver: "90.3da40e9"
---

# Installing scanless

> Utility for using websites that can perform port scans on your behalf.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/scanless) (version `90.3da40e9`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S scanless    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/vesche/scanless) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `beautifulsoup4`, `bs4`, `crayons`, `pip`, `requests`, `setuptools`, `wheel`
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

Backlinks: [[scanner]] · [[scanless]] · [[BlackArch Tools]]

