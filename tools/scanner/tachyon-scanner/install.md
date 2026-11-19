---
tool: "tachyon-scanner"
build-system: "python"
upstream: "https://github.com/delvelabs/tachyon"
recipe: "blackarch"
source: "https://github.com/delvelabs/tachyon"
license: "custom:unknown"
blackarch-pkgver: "3.5.6.r4.gba0fe61"
provides: ["tachyon", "tachyon-scanner"]
---

# Installing tachyon-scanner

> Fast Multi-Threaded Web Discovery Tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/tachyon-scanner) (version `3.5.6.r4.gba0fe61`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S tachyon-scanner    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/delvelabs/tachyon.git
cd tachyon
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `click`, `easyinject`, `hammertime-http`, `pip`, `setuptools`, `urllib3`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Exact build steps from the recipe

```bash
cd $pkgname

  python setup.py build
```

## Installed layout (per recipe)

- Commands: `/usr/bin/tachyon`, `/usr/bin/tachyon-scanner`

Backlinks: [[scanner]] · [[tachyon-scanner]] · [[BlackArch Tools]]

