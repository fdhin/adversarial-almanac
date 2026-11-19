---
tool: "device-pharmer"
build-system: "scripts"
upstream: "https://github.com/DanMcInerney/device-pharmer"
recipe: "blackarch"
source: "https://github.com/DanMcInerney/device-pharmer"
license: "MIT"
blackarch-pkgver: "40.b06a460"
---

# Installing device-pharmer

> Opens 1K+ IPs or Shodan search results and attempts to login.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/device-pharmer) (version `40.b06a460`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S device-pharmer    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/DanMcInerney/device-pharmer) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-beautifulsoup4, python2-gevent, python2-mechanize, python2-setuptools, python2-shodan (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[cracker]] · [[device-pharmer]] · [[BlackArch Tools]]

