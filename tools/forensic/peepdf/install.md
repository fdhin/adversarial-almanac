---
tool: "peepdf"
build-system: "python"
upstream: "https://eternal-todo.com/tools/peepdf-pdf-analysis-tool"
recipe: "blackarch"
aur-package: "peepdf"
source: "https://eternal-todo.com/tools/peepdf-pdf-analysis-tool"
license: "GPL-3.0-or-later"
blackarch-pkgver: "0.4.2"
---

# Installing peepdf

> A Python tool to explore PDF files in order to find out if the file can be harmful or not.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/peepdf) (version `0.4.2`, license GPL-3.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S peepdf    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://eternal-todo.com/tools/peepdf-pdf-analysis-tool) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `setuptools`
- ⚠️ Python 2 only deps: python2-setuptools (legacy; needs python2)

## Exact build steps from the recipe

```bash
cd "$pkgbase-$pkgver-2"

  python2 setup.py build

  cd "$srcdir/$pkgbase-$pkgver"

  python setup.py build
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[forensic]] · [[peepdf]] · [[BlackArch Tools]]

