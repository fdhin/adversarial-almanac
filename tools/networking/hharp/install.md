---
tool: "hharp"
build-system: "scripts"
upstream: "http://packetstormsecurity.com/files/81368/Hackers-Hideaway-ARP-Attack-Tool.html"
recipe: "blackarch"
source: "https://dl.packetstormsecurity.net/UNIX/utilities/hharp.py.tar.bz2"
license: "custom:unknown"
blackarch-pkgver: "1beta"
---

# Installing hharp

> This tool can perform man-in-the-middle and switch flooding attacks. It has 4 major functions, 3 of which attempt to man-in-the-middle one or more computers on a network with a passive method or flood type method.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/hharp) (version `1beta`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S hharp    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
curl -fLO https://dl.packetstormsecurity.net/UNIX/utilities/hharp.py.tar.bz2
tar xf hharp.py.tar.bz2
cd hharp.py
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-scapy (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[networking]] · [[hharp]] · [[BlackArch Tools]]

