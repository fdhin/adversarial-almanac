---
tool: "social-analyzer"
build-system: "python"
upstream: "https://pypi.org/project/social-analyzer/"
recipe: "blackarch"
source: "https://pypi.org/project/social-analyzer/"
license: "AGPL3"
blackarch-pkgver: "0.45"
---

# Installing social-analyzer

> Analyzing & finding a person's profile across social media websites.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/social-analyzer) (version `0.45`, license AGPL3).

## Arch / BlackArch

```bash
sudo pacman -S social-analyzer    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://pypi.org/project/social-analyzer/) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `beautifulsoup4`, `galeodes`, `langdetect`, `lxml`, `requests`, `setuptools`, `termcolor`, `tld`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Exact build steps from the recipe

```bash
cd "$pkgname-$pkgver"

  python setup.py build
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[social]] · [[social-analyzer]] · [[BlackArch Tools]]

