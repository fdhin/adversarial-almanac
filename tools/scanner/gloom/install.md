---
tool: "gloom"
build-system: "scripts"
upstream: "https://github.com/StreetSec/Gloom-Framework"
recipe: "blackarch"
source: "https://github.com/StreetSec/Gloom-Framework"
license: "GPL3"
blackarch-pkgver: "93.cd6e927"
---

# Installing gloom

> Linux Penetration Testing Framework.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/gloom) (version `93.cd6e927`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S gloom    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/StreetSec/Gloom-Framework.git
cd Gloom-Framework
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `curl`
  - `nmap`
  - `python2`
  - `wifijammer`
- ⚠️ Python 2 only deps: python2-beautifulsoup4, python2-dateutil, python2-geoip, python2-mechanize, python2-pythonwhois, python2-requests, python2-scapy, python2-termcolor (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[gloom]] · [[BlackArch Tools]]

