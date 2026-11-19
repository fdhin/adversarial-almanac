---
tool: "ikeforce"
build-system: "scripts"
upstream: "https://github.com/SpiderLabs/ikeforce"
recipe: "blackarch"
source: "https://github.com/SpiderLabs/ikeforce"
license: "custom:unknown"
blackarch-pkgver: "30.575af15"
---

# Installing ikeforce

> A command line IPSEC VPN brute forcing tool for Linux that allows group name/ID enumeration and XAUTH brute forcing capabilities.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/ikeforce) (version `30.575af15`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S ikeforce    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/SpiderLabs/ikeforce) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-pycryptodome, python2-pycryptodomex, python2-pyip, python2-pyopenssl (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[cracker]] · [[ikeforce]] · [[BlackArch Tools]]

