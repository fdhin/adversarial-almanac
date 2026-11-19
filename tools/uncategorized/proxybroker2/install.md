---
tool: "proxybroker2"
build-system: "python"
upstream: "https://github.com/bluet/proxybroker2"
recipe: "blackarch"
source: "https://github.com/bluet/proxybroker2"
license: "Apache"
blackarch-pkgver: "663.bbf753d"
---

# Installing proxybroker2

> Proxy [Finder

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/proxybroker2) (version `663.bbf753d`, license Apache).

## Arch / BlackArch

```bash
sudo pacman -S proxybroker2    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the  Server]. HTTP(S) & SOCKS.| blackarch-proxy |https://github.com/bluet/proxybroker2 (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `aiodns`, `aiohttp`, `attrs`, `cachetools`, `maxminddb`, `pip`, `setuptools`, `wheel`
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

Backlinks: [[uncategorized]] · [[proxybroker2]] · [[BlackArch Tools]]

