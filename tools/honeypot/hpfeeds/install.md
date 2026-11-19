---
tool: "hpfeeds"
build-system: "python"
upstream: "https://github.com/rep/hpfeeds"
recipe: "blackarch"
source: "https://github.com/rep/hpfeeds"
license: "BSD"
blackarch-pkgver: "414.34bf4d4"
---

# Installing hpfeeds

> Honeynet Project generic authenticated datafeed protocol.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/hpfeeds) (version `414.34bf4d4`, license BSD).

## Arch / BlackArch

```bash
sudo pacman -S hpfeeds    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/rep/hpfeeds) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `setuptools`
- **Build tools**: `git`
- ⚠️ Python 2 only deps: python2-setuptools (legacy; needs python2)

## Exact build steps from the recipe

```bash
cd "$pkgbase-2"

  python2 setup.py build

  cd "$srcdir/$pkgbase"

  python setup.py build
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[honeypot]] · [[hpfeeds]] · [[BlackArch Tools]]

