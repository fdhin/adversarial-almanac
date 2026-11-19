---
tool: "nbnspoof"
build-system: "scripts"
upstream: "http://www.mcgrewsecurity.com/tools/nbnspoof/"
recipe: "blackarch"
source: "http://www.mcgrewsecurity.com/tools/nbnspoof/"
license: "GPL"
blackarch-pkgver: "1.0"
---

# Installing nbnspoof

> NetBIOS Name Service Spoofer.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/nbnspoof) (version `1.0`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S nbnspoof    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://www.mcgrewsecurity.com/tools/nbnspoof/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-scapy (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[spoof]] · [[nbnspoof]] · [[BlackArch Tools]]

