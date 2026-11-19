---
tool: "ttpassgen"
build-system: "python"
upstream: "https://github.com/tp7309/TTPassGen"
recipe: "blackarch"
source: "https://github.com/tp7309/TTPassGen"
license: "Apache"
blackarch-pkgver: "133.a06d99d"
---

# Installing ttpassgen

> Highly flexible and scriptable password dictionary generator based on Python.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/ttpassgen) (version `133.a06d99d`, license Apache).

## Arch / BlackArch

```bash
sudo pacman -S ttpassgen    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/tp7309/TTPassGen) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `click`, `setuptools`, `tqdm`
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

Backlinks: [[automation]] · [[ttpassgen]] · [[BlackArch Tools]]

