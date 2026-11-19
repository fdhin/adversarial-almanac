---
tool: "dga-detection"
build-system: "scripts"
upstream: "https://github.com/philarkwright/DGA-Detection"
recipe: "blackarch"
source: "https://github.com/philarkwright/DGA-Detection"
license: "BSD"
blackarch-pkgver: "78.0a3186e"
---

# Installing dga-detection

> DGA Domain Detection using Bigram Frequency Analysis.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/dga-detection) (version `78.0a3186e`, license BSD).

## Arch / BlackArch

```bash
sudo pacman -S dga-detection    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/philarkwright/DGA-Detection
cd DGA-Detection
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-alexa-top-sites, python2-argparse, python2-pushbullet.py, python2-scapy, python2-scipy, python2-tldextract, python2-websocket-client (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[dga-detection]] · [[BlackArch Tools]]

