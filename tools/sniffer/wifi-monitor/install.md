---
tool: "wifi-monitor"
build-system: "scripts"
upstream: "https://github.com/DanMcInerney/wifi-monitor"
recipe: "blackarch"
source: "https://github.com/DanMcInerney/wifi-monitor"
license: "custom"
blackarch-pkgver: "30.0657e48"
---

# Installing wifi-monitor

> Prints the IPs on your local network that're sending the most packets.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/wifi-monitor) (version `30.0657e48`, license custom).

## Arch / BlackArch

```bash
sudo pacman -S wifi-monitor    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/DanMcInerney/wifi-monitor) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `aircrack-ng`
  - `nbtscan`
  - `python2`
- ⚠️ Python 2 only deps: python2-scapy (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[sniffer]] · [[wifi-monitor]] · [[BlackArch Tools]]

