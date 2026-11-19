---
tool: "facebrute"
build-system: "scripts"
upstream: "https://github.com/emerinohdz/FaceBrute"
recipe: "blackarch"
source: "https://github.com/emerinohdz/FaceBrute"
license: "Artistic"
blackarch-pkgver: "7.ece355b"
---

# Installing facebrute

> This script tries to guess passwords for a given facebook account using a list of passwords (dictionary).

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/facebrute) (version `7.ece355b`, license Artistic).

## Arch / BlackArch

```bash
sudo pacman -S facebrute    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/emerinohdz/FaceBrute.git
cd FaceBrute
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-httplib2 (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[cracker]] · [[facebrute]] · [[BlackArch Tools]]

