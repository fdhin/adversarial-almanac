---
tool: "omnibus"
build-system: "scripts"
upstream: "https://github.com/InQuest/omnibus"
recipe: "blackarch"
source: "https://github.com/InQuest/omnibus"
license: "MIT"
blackarch-pkgver: "129.88dbf5d"
---

# Installing omnibus

> OSINT tool for intelligence collection, research and artifact management.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/omnibus) (version `129.88dbf5d`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S omnibus    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/InQuest/omnibus) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `mongodb`
  - `python2`
  - `redis`
- ⚠️ Python 2 only deps: python2-backports.functools_lru_cache, python2-beautifulsoup3, python2-censys, python2-cmd2, python2-contextlib2, python2-cymon, python2-dnspython, python2-fuzzyfinder, python2-ipwhois, python2-pygments, python2-pymongo, python2-redis, python2-requests, python2-threatcrowd (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[omnibus]] · [[BlackArch Tools]]

