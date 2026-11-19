---
tool: "python-search-engine-parser"
build-system: "python"
upstream: "https://pypi.org/project/search-engine-parser/#files"
recipe: "blackarch"
source: "https://pypi.org/project/search-engine-parser/#files"
license: "MIT"
blackarch-pkgver: "0.6.8"
---

# Installing python-search-engine-parser

> Scrapes search engine pages for query titles, descriptions and links.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/python-search-engine-parser) (version `0.6.8`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S python-search-engine-parser    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://pypi.org/project/search-engine-parser/#files) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `aiohttp`, `beautifulsoup4`, `blessed`, `lxml`, `setuptools`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Exact build steps from the recipe

```bash
cd "$_pkgname-$pkgver"

  python setup.py build
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[uncategorized]] · [[python-search-engine-parser]] · [[BlackArch Tools]]

