---
tool: "doozer"
build-system: "scripts"
upstream: "https://github.com/CoalfireLabs/crackHOR"
recipe: "blackarch"
source: "https://github.com/CoalfireLabs/crackHOR"
license: "GPL2"
blackarch-pkgver: "9.5cfc8f8"
provides: ["doozer-monitor", "doozestop"]
---

# Installing doozer

> A Password cracking utility.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/doozer) (version `9.5cfc8f8`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S doozer    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/CoalfireLabs/Doozer.git
cd Doozer
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `hashcat`
  - `ophcrack`
  - `python2`
- ⚠️ Python 2 only deps: python2-django, python2-requests (legacy; needs python2)

## Installed layout (per recipe)

- Commands: `/usr/bin/doozer-monitor`, `/usr/bin/doozestop`

Backlinks: [[cracker]] · [[doozer]] · [[BlackArch Tools]]

