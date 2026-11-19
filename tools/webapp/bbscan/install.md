---
tool: "bbscan"
build-system: "scripts"
upstream: "https://github.com/lijiejie/bbscan"
recipe: "blackarch"
source: "https://github.com/lijiejie/bbscan"
license: "Apache"
blackarch-pkgver: "53.29b9f11"
---

# Installing bbscan

> A tiny Batch web vulnerability Scanner.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/bbscan) (version `53.29b9f11`, license Apache).

## Arch / BlackArch

```bash
sudo pacman -S bbscan    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/lijiejie/BBScan.git
cd BBScan
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-beautifulsoup4, python2-gevent, python2-ipaddress, python2-requests, python2-urllib3 (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[bbscan]] · [[BlackArch Tools]]

