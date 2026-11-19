---
tool: "stringsifter"
build-system: "python"
upstream: "https://github.com/fireeye/stringsifter"
recipe: "blackarch"
source: "https://github.com/fireeye/stringsifter"
license: "Apache"
blackarch-pkgver: "39.33c0cd5"
---

# Installing stringsifter

> Machine learning tool that automatically ranks strings based on their relevance for malware analysis.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/stringsifter) (version `39.33c0cd5`, license Apache).

## Arch / BlackArch

```bash
sudo pacman -S stringsifter    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/fireeye/stringsifter) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `build`, `joblib`, `numpy`, `pip`, `scikit-learn`
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

Backlinks: [[binary]] · [[stringsifter]] · [[BlackArch Tools]]

