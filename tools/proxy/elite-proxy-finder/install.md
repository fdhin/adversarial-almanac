---
tool: "elite-proxy-finder"
build-system: "scripts"
upstream: "https://github.com/DanMcInerney/elite-proxy-finder"
recipe: "blackarch"
source: "https://github.com/DanMcInerney/elite-proxy-finder"
license: "custom"
blackarch-pkgver: "51.1ced3be"
---

# Installing elite-proxy-finder

> Finds public elite anonymity proxies and concurrently tests them.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/elite-proxy-finder) (version `51.1ced3be`, license custom).

## Arch / BlackArch

```bash
sudo pacman -S elite-proxy-finder    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/DanMcInerney/elite-proxy-finder) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-beautifulsoup3, python2-gevent, python2-requests, python2-setuptools (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[proxy]] · [[elite-proxy-finder]] · [[BlackArch Tools]]

