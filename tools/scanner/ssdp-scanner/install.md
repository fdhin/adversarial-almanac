---
tool: "ssdp-scanner"
build-system: "scripts"
upstream: "http://packetstormsecurity.com/files/127994/SSDP-Amplification-Scanner.html"
recipe: "blackarch"
source: "http://packetstormsecurity.com/files/127994/SSDP-Amplification-Scanner.html"
license: "custom:unknown"
blackarch-pkgver: "1.0"
---

# Installing ssdp-scanner

> SSDP amplification scanner written in Python. Makes use of Scapy.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/ssdp-scanner) (version `1.0`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S ssdp-scanner    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://packetstormsecurity.com/files/127994/SSDP-Amplification-Scanner.html) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-scapy (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[ssdp-scanner]] · [[BlackArch Tools]]

