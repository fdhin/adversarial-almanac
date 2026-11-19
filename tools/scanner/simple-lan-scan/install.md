---
tool: "simple-lan-scan"
build-system: "scripts"
upstream: "http://packetstormsecurity.com/files/97353/Simple-LAN-Scanner-1.0.html"
recipe: "blackarch"
source: "http://packetstormsecurity.com/files/97353/Simple-LAN-Scanner-1.0.html"
license: "custom:unknown"
blackarch-pkgver: "1.0"
---

# Installing simple-lan-scan

> A simple python script that leverages scapy for discovering live hosts on a network.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/simple-lan-scan) (version `1.0`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S simple-lan-scan    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://packetstormsecurity.com/files/97353/Simple-LAN-Scanner.0.html) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-scapy (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[simple-lan-scan]] · [[BlackArch Tools]]

