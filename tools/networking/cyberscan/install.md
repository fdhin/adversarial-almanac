---
tool: "cyberscan"
build-system: "scripts"
upstream: "https://github.com/medbenali/CyberScan"
recipe: "blackarch"
source: "https://github.com/medbenali/CyberScan"
license: "GPL3"
blackarch-pkgver: "87.24c69b9"
---

# Installing cyberscan

> A Network Pentesting Tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/cyberscan) (version `87.24c69b9`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S cyberscan    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/medbenali/CyberScan.git
cd CyberScan
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-argparse, python2-asn1crypto, python2-pygeoip, python2-scapy (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[networking]] · [[cyberscan]] · [[BlackArch Tools]]

