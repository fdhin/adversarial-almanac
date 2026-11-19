---
tool: "chiron"
build-system: "scripts"
upstream: "http://www.secfu.net/tools-scripts/"
recipe: "blackarch"
source: "http://www.secfu.net/tools-scripts/"
license: "GPL2"
blackarch-pkgver: "48.524abe1"
---

# Installing chiron

> An all-in-one IPv6 Penetration Testing Framework.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/chiron) (version `48.524abe1`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S chiron    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/aatlasis/Chiron.git
cd Chiron
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-ipaddr, python2-netaddr, python2-scapy (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[chiron]] · [[BlackArch Tools]]

