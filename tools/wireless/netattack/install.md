---
tool: "netattack"
build-system: "scripts"
upstream: "https://github.com/chrizator/netattack2"
recipe: "blackarch"
source: "https://github.com/chrizator/netattack2"
license: "MIT"
blackarch-pkgver: "24.230b856"
---

# Installing netattack

> Python script to scan and attack wireless networks.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/netattack) (version `24.230b856`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S netattack    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/chrizator/netattack2.git
cd netattack2
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `iw`
  - `python2`
  - `wireless_tools` — Debian wireless-tools / Fedora wireless-tools
- ⚠️ Python 2 only deps: python2-argparse, python2-scapy (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[wireless]] · [[netattack]] · [[BlackArch Tools]]

