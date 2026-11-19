---
tool: "recollapse"
build-system: "python"
upstream: "https://github.com/0xacb/recollapse"
recipe: "blackarch"
source: "https://github.com/0xacb/recollapse"
license: "MIT"
blackarch-pkgver: "1.0.r1.g090d711"
---

# Installing recollapse

> Tool for black-box regex fuzzing to bypass validations and discover normalizations in web applications.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/recollapse) (version `1.0.r1.g090d711`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S recollapse    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/0xacb/recollapse) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `build`, `pip`, `prettytable`, `unidecode`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Exact build steps from the recipe

```bash
cd $pkgname

  python -m build --wheel --outdir="$startdir/dist"
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[recollapse]] · [[BlackArch Tools]]

