---
tool: "onionsearch"
build-system: "python"
upstream: "https://github.com/megadose/OnionSearch"
recipe: "blackarch"
source: "https://github.com/megadose/OnionSearch"
license: "GPL-3.0-only"
blackarch-pkgver: "44.fc9d62c"
---

# Installing onionsearch

> Script that scrapes urls on different .onion search engines.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/onionsearch) (version `44.fc9d62c`, license GPL-3.0-only).

## Arch / BlackArch

```bash
sudo pacman -S onionsearch    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/megadose/OnionSearch.git
cd OnionSearch
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `beautifulsoup4`, `build`, `html5lib`, `installer`, `pysocks`, `requests`, `setuptools`, `termcolor`, `tqdm`, `wheel`
- **Build tools**: `git`

## Exact build steps from the recipe

```bash
cd $pkgname

  python -m build --wheel --no-isolation
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[onionsearch]] · [[BlackArch Tools]]

