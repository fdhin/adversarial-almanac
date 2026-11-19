---
tool: "dorkbot"
build-system: "python"
upstream: "https://github.com/utiso/dorkbot"
recipe: "blackarch"
source: "https://github.com/utiso/dorkbot"
license: "custom:unknown"
blackarch-pkgver: "437.2b61966"
---

# Installing dorkbot

> Command-line tool to scan Google search results for vulnerabilities.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/dorkbot) (version `437.2b61966`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S dorkbot    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/utiso/dorkbot) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `build`, `pip`
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

Backlinks: [[scanner]] · [[dorkbot]] · [[BlackArch Tools]]

