---
tool: "wifijammer"
build-system: "scripts"
upstream: "https://github.com/DanMcInerney/wifijammer"
recipe: "blackarch"
source: "https://github.com/DanMcInerney/wifijammer"
license: "GPL2"
blackarch-pkgver: "100.be05dab"
---

# Installing wifijammer

> A python script to continuously jam all wifi clients within range.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/wifijammer) (version `100.be05dab`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S wifijammer    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/DanMcInerney/wifijammer) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-scapy, python2-setuptools (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[wireless]] · [[wifijammer]] · [[BlackArch Tools]]

