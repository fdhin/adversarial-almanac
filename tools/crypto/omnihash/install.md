---
tool: "omnihash"
build-system: "scripts"
upstream: "https://github.com/Miserlou/omnihash"
recipe: "blackarch"
source: "https://github.com/Miserlou/omnihash"
license: "MIT"
blackarch-pkgver: "70.870e9ae"
provides: ["oh"]
---

# Installing omnihash

> Hash files, strings, input streams and network resources in various common algorithms simultaneously.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/omnihash) (version `70.870e9ae`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S omnihash    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/Miserlou/omnihash) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-certifi, python2-click, python2-colorama, python2-crcmod, python2-pyblake2, python2-requests, python2-setuptools, python2-sha3, python2-validators (legacy; needs python2)

## Installed layout (per recipe)

- Commands: `/usr/bin/oh`

Backlinks: [[crypto]] · [[omnihash]] · [[BlackArch Tools]]

