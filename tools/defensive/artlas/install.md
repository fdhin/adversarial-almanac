---
tool: "artlas"
build-system: "scripts"
upstream: "https://github.com/mthbernardes/ARTLAS"
recipe: "blackarch"
source: "https://github.com/mthbernardes/ARTLAS"
license: "MIT"
blackarch-pkgver: "154.e5fdd8d"
---

# Installing artlas

> Apache Real Time Logs Analyzer System.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/artlas) (version `154.e5fdd8d`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S artlas    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/mthbernardes/ARTLAS.git
cd ARTLAS
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-apache-log-parser, python2-py-zabbix, python2-pygtail, python2-requests, python2-six, python2-telepot, python2-ua-parser, python2-urllib3, python2-user-agents (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[defensive]] · [[artlas]] · [[BlackArch Tools]]

