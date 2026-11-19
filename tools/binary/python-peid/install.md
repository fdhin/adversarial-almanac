---
tool: "python-peid"
build-system: "python"
upstream: ""
recipe: "blackarch"
source: ""
license: "GPL3"
blackarch-pkgver: "2.4.0"
---

# Installing python-peid

> Python implementation of the Packed Executable iDentifier (PEiD).

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/python-peid) (version `2.4.0`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S python-peid    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the the upstream site (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `build`, `pefile`, `pip`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Exact build steps from the recipe

```bash
cd "$_pkgname-$pkgver"

  python -m build --wheel --outdir="$startdir/dist"
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[binary]] · [[python-peid]] · [[BlackArch Tools]]

