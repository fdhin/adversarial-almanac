---
tool: "twint"
build-system: "python"
upstream: "https://github.com/twintproject/twint"
recipe: "blackarch"
source: "https://github.com/twintproject/twint"
license: "MIT"
blackarch-pkgver: "845.e7c8a0c7"
---

# Installing twint

> An advanced Twitter scraping & OSINT tool written in Python that doesn't use Twitter's API, allowing you to scrape a user's followers, following, Tweets and more while evading most API limitations.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/twint) (version `845.e7c8a0c7`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S twint    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/twintproject/twint) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `aiodns`, `aiohttp`, `aiohttp-socks`, `beautifulsoup4`, `cchardet`, `elasticsearch`, `fake-useragent`, `geopy`, `googletransx`, `pandas`, `pysocks`, `schedule`, `setuptools`
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

Backlinks: [[social]] · [[twint]] · [[BlackArch Tools]]

