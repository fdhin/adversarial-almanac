---
tool: "websearch"
build-system: "scripts"
upstream: "https://github.com/PentesterES/WebSearch"
recipe: "blackarch"
source: "https://github.com/PentesterES/WebSearch"
license: "custom:unknown"
blackarch-pkgver: "4.cb7ef8e"
---

# Installing websearch

> Search vhost names given a host range. Powered by Bing..

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/websearch) (version `4.cb7ef8e`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S websearch    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/PentesterES/WebSearch.git
cd WebSearch
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-simplejson (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[websearch]] · [[BlackArch Tools]]

