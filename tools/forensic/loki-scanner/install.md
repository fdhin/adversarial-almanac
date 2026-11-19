---
tool: "loki-scanner"
build-system: "scripts"
upstream: "https://github.com/Neo23x0/Loki"
recipe: "blackarch"
source: "https://github.com/Neo23x0/Loki"
license: "GPL3"
blackarch-pkgver: "1258.7688421"
---

# Installing loki-scanner

> Simple IOC and Incident Response Scanner.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/loki-scanner) (version `1258.7688421`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S loki-scanner    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/Neo23x0/Loki.git
cd Loki
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-argparse, python2-beautifulsoup4, python2-colorama, python2-helpers, python2-ipy, python2-netaddr, python2-psutil, python2-pycryptodome, python2-pycurl, python2-pylzma, python2-pytz, python2-requests, python2-rfc5424-logging-handler, python2-simplejson, python2-tzlocal, python2-yara (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[forensic]] · [[loki-scanner]] · [[BlackArch Tools]]

