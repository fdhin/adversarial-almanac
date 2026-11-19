---
tool: "pidense"
build-system: "scripts"
upstream: "https://github.com/WiPi-Hunter/PiDense"
recipe: "blackarch"
source: "https://github.com/WiPi-Hunter/PiDense"
license: "GPL3"
blackarch-pkgver: "29.ef26704"
---

# Installing pidense

> Monitor illegal wireless network activities. (Fake Access Points)

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/pidense) (version `29.ef26704`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S pidense    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/WiPi-Hunter/PiDense.git
cd PiDense
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-argparse, python2-scapy, python2-termcolor (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[wireless]] · [[pidense]] · [[BlackArch Tools]]

