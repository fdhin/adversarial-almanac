---
tool: "cisco-snmp-slap"
build-system: "scripts"
upstream: "https://github.com/nccgroup/cisco-snmp-slap"
recipe: "blackarch"
source: "https://github.com/nccgroup/cisco-snmp-slap"
license: "GPL3"
blackarch-pkgver: "5.daf0589"
---

# Installing cisco-snmp-slap

> IP address spoofing tool in order to bypass an ACL protecting an SNMP service on Cisco IOS devices.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/cisco-snmp-slap) (version `5.daf0589`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S cisco-snmp-slap    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/nccgroup/cisco-snmp-slap) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-scapy (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[spoof]] · [[cisco-snmp-slap]] · [[BlackArch Tools]]

