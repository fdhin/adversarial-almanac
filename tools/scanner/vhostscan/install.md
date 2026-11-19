---
tool: "vhostscan"
build-system: "python"
upstream: "https://github.com/codingo/VHostScan"
recipe: "blackarch"
source: "https://github.com/codingo/VHostScan"
license: "GPL-3.0-or-later"
blackarch-pkgver: "1.21.r68.gacc7890"
provides: ["VHostScan"]
---

# Installing vhostscan

> A virtual host scanner that can be used with pivot tools, detect catch-all scenarios, aliases and dynamic default pages.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/vhostscan) (version `1.21.r68.gacc7890`, license GPL-3.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S vhostscan    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/codingo/VHostScan.git
cd VHostScan
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `dnspython`, `fuzzywuzzy`, `numpy`, `pandas`, `pip`, `requests`, `setuptools`, `simplejson`, `urllib3`, `wheel`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Exact build steps from the recipe

```bash
cd $pkgname

  python setup.py build
```

## Installed layout (per recipe)

- Commands: `/usr/bin/VHostScan`

Backlinks: [[scanner]] · [[vhostscan]] · [[BlackArch Tools]]

