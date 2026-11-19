---
tool: "dnsgen"
build-system: "python"
upstream: "https://github.com/AlephNullSK/dnsgen"
recipe: "blackarch"
source: "https://github.com/AlephNullSK/dnsgen"
license: "MIT"
blackarch-pkgver: "v1.0.4.r12.g7c98e7e"
---

# Installing dnsgen

> Generate combination of domain names from the provided input.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/dnsgen) (version `v1.0.4.r12.g7c98e7e`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S dnsgen    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/AlephNullSK/dnsgen) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `build`, `click`, `pip`, `rich`, `tldextract`
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

Backlinks: [[misc]] · [[dnsgen]] · [[BlackArch Tools]]

