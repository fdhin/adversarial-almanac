---
tool: "netscan2"
build-system: "scripts"
upstream: "https://github.com/walchko/netscan2"
recipe: "blackarch"
source: "https://github.com/walchko/netscan2"
license: "MIT"
blackarch-pkgver: "60.3d02ba1"
---

# Installing netscan2

> Active / passive network scanner.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/netscan2) (version `60.3d02ba1`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S netscan2    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/walchko/netscan2) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `mitmproxy`
  - `python2`
- ⚠️ Python 2 only deps: python2-awake, python2-build-utils, python2-dpkt, python2-netaddr, python2-nose, python2-pcapy, python2-requests, python2-setuptools, python2-simplejson (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[netscan2]] · [[BlackArch Tools]]

