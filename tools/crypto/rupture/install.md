---
tool: "rupture"
build-system: "scripts"
upstream: "https://github.com/decrypto-org/rupture"
recipe: "blackarch"
source: "https://github.com/decrypto-org/rupture"
license: "MIT"
blackarch-pkgver: "1383.131c61a"
---

# Installing rupture

> A framework for BREACH and other compression-based crypto attacks.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/rupture) (version `1383.131c61a`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S rupture    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/decrypto-org/rupture) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`, `npm`
- **System libraries** (Arch name — translate for your distro):
  - `bash`
  - `bettercap`
  - `python2`
  - `scapy`
- ⚠️ Python 2 only deps: python2-beautifulsoup4, python2-coverage, python2-coveralls, python2-django, python2-django-nose, python2-ipaddress, python2-mock, python2-netifaces, python2-pycodestyle, python2-requests, python2-tlslite-ng, python2-yaml (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[crypto]] · [[rupture]] · [[BlackArch Tools]]

