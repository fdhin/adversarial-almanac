---
tool: "python-trackerjacker"
build-system: "python"
upstream: "https://github.com/calebmadrigal/trackerjacker"
recipe: "blackarch"
source: "https://github.com/calebmadrigal/trackerjacker"
license: "MIT"
blackarch-pkgver: "2.0.5"
---

# Installing python-trackerjacker

> Finds and tracks wifi devices through raw 802.11 monitoring.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/python-trackerjacker) (version `2.0.5`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S python-trackerjacker    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/calebmadrigal/trackerjacker) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `build`, `pip`, `pyaml`, `ruamel-yaml`, `scapy`, `wheel`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Exact build steps from the recipe

```bash
cd "$_pkgname-$pkgver"

  python -m build --wheel --outdir="$startdir/dist"
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[wireless]] · [[python-trackerjacker]] · [[BlackArch Tools]]

