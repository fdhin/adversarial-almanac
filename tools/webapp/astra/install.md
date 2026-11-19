---
tool: "astra"
build-system: "scripts"
upstream: "https://github.com/flipkart-incubator/astra"
recipe: "blackarch"
aur-package: "astra"
source: "https://github.com/flipkart-incubator/astra"
license: "Apache"
blackarch-pkgver: "489.092804a"
---

# Installing astra

> Automated Security Testing For REST API's.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/astra) (version `489.092804a`, license Apache).

## Arch / BlackArch

```bash
sudo pacman -S astra    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/flipkart-incubator/astra) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `mongodb`
  - `python2`
  - `sqlmap`
- ⚠️ Python 2 only deps: python2-argparse, python2-flask, python2-pyjwt, python2-pymongo, python2-requests (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[astra]] · [[BlackArch Tools]]

