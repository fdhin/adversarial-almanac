---
tool: "deepzero"
build-system: "python"
upstream: "https://github.com/416rehman/DeepZero"
recipe: "blackarch"
source: "https://github.com/416rehman/DeepZero"
license: "MIT"
blackarch-pkgver: "deepzero.v0.4.0.r4.g1bf1818"
---

# Installing deepzero

> Find zero-days while you sleep. An automated vulnerability research framework.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/deepzero) (version `deepzero.v0.4.0.r4.g1bf1818`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S deepzero    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/416rehman/DeepZero) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `build`, `click`, `dotenv`, `installer`, `jinja`, `rich`, `setuptools`, `wheel`, `yaml`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Exact build steps from the recipe

```bash
cd $pkgname

  python -m build --wheel --no-isolation
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[ai]] · [[deepzero]] · [[BlackArch Tools]]

