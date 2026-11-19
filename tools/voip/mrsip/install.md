---
tool: "mrsip"
build-system: "scripts"
upstream: "https://github.com/meliht/mr.sip"
recipe: "blackarch"
source: "https://github.com/meliht/mr.sip"
license: "GPL3"
blackarch-pkgver: "113.0be6d2e"
---

# Installing mrsip

> SIP-Based Audit and Attack Tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/mrsip) (version `113.0be6d2e`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S mrsip    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/meliht/mr.sip.git
cd mr.sip
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
  - `toilet`
- ⚠️ Python 2 only deps: python2-ipaddress, python2-netifaces, python2-pyfiglet, python2-scapy (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[voip]] · [[mrsip]] · [[BlackArch Tools]]

