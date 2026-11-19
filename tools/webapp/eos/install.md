---
tool: "eos"
build-system: "python"
upstream: "https://github.com/synacktiv/eos"
recipe: "blackarch"
source: "https://github.com/synacktiv/eos"
license: "custom:THE BEER-WARE"
blackarch-pkgver: "16.47f0086"
---

# Installing eos

> Enemies Of Symfony - Debug mode Symfony looter.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/eos) (version `16.47f0086`, license custom:THE BEER-WARE).

## Arch / BlackArch

```bash
sudo pacman -S eos    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/synacktiv/eos) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `beautifulsoup4`, `defusedxml`, `requests`, `setuptools`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Exact build steps from the recipe

```bash
cd $pkgname

  python setup.py build
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[eos]] · [[BlackArch Tools]]

