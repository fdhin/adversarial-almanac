---
tool: "detect-sniffer"
build-system: "scripts"
upstream: "https://github.com/galkan/tools/tree/master/detect_sniffer"
recipe: "blackarch"
source: "https://github.com/galkan/tools"
license: "custom:unknown"
blackarch-pkgver: "159.a359262"
---

# Installing detect-sniffer

> Tool that detects sniffers in the network.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/detect-sniffer) (version `159.a359262`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S detect-sniffer    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/galkan/tools
cd tools
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-ldap, python2-scapy (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[defensive]] · [[detect-sniffer]] · [[BlackArch Tools]]

