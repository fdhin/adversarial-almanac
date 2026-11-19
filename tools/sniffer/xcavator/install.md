---
tool: "xcavator"
build-system: "scripts"
upstream: "https://github.com/nccgroup/xcavator"
recipe: "blackarch"
source: "https://github.com/nccgroup/xcavator"
license: "GPL3"
blackarch-pkgver: "5.bd9e2d8"
---

# Installing xcavator

> Man-In-The-Middle and phishing attack tool that steals the victim's credentials of some web services like Facebook.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/xcavator) (version `5.bd9e2d8`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S xcavator    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/nccgroup/xcavator) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-configparser, python2-ipaddr, python2-ipaddress, python2-pysmb, python2-pysmbc (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[sniffer]] · [[xcavator]] · [[BlackArch Tools]]

