---
tool: "sslcaudit"
build-system: "scripts"
upstream: "https://github.com/grwl/sslcaudit"
recipe: "blackarch"
source: "https://github.com/grwl/sslcaudit"
license: "GPL3"
blackarch-pkgver: "524.f218b9b"
---

# Installing sslcaudit

> Utility to perform security audits of SSL/TLS clients.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/sslcaudit) (version `524.f218b9b`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S sslcaudit    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/grwl/sslcaudit) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-m2crypto, python2-setuptools (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[sslcaudit]] · [[BlackArch Tools]]

