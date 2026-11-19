---
tool: "sergio-proxy"
build-system: "scripts"
upstream: "https://github.com/supernothing/sergio-proxy"
recipe: "blackarch"
source: "https://github.com/supernothing/sergio-proxy"
license: "GPL3"
blackarch-pkgver: "20.8a91bb4"
---

# Installing sergio-proxy

> A multi-threaded transparent HTTP proxy for manipulating web traffic.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/sergio-proxy) (version `20.8a91bb4`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S sergio-proxy    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/supernothing/sergio-proxy) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-constantly, python2-pyopenssl, python2-twistedx (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[proxy]] · [[sergio-proxy]] · [[BlackArch Tools]]

