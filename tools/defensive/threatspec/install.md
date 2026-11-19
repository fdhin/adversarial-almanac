---
tool: "threatspec"
build-system: "python"
upstream: "https://github.com/threatspec/threatspec"
recipe: "blackarch"
source: "https://github.com/threatspec/threatspec"
license: "MIT"
blackarch-pkgver: "0.5.0"
---

# Installing threatspec

> Project to integrate threat modelling into development process.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/threatspec) (version `0.5.0`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S threatspec    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/threatspec/threatspec) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `click`, `comment-parser`, `graphviz`, `jinja`, `jsonschema`, `magic`, `pip`, `setuptools-scm`, `wheel`, `yaml`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  python setup.py build
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[defensive]] · [[threatspec]] · [[BlackArch Tools]]

