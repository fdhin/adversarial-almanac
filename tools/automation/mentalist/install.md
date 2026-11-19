---
tool: "mentalist"
build-system: "python"
upstream: "https://github.com/sc0tfree/mentalist"
recipe: "blackarch"
source: "https://github.com/sc0tfree/mentalist"
license: "MIT"
blackarch-pkgver: "v2.0.0.r1.g8223d8e"
---

# Installing mentalist

> Graphical tool for custom wordlist generation.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/mentalist) (version `v2.0.0.r1.g8223d8e`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S mentalist    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/sc0tfree/mentalist) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `build`, `pip`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`
  - `tk`

## Exact build steps from the recipe

```bash
cd $pkgname

  python -m build --wheel --outdir="$startdir/dist"
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[automation]] · [[mentalist]] · [[BlackArch Tools]]

