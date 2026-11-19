---
tool: "atear"
build-system: "scripts"
upstream: "https://github.com/NORMA-Inc/AtEar"
recipe: "blackarch"
source: "https://github.com/NORMA-Inc/AtEar"
license: "Apache"
blackarch-pkgver: "139.245ec8d"
---

# Installing atear

> Wireless Hacking, WiFi Security, Vulnerability Analyzer, Pentestration.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/atear) (version `139.245ec8d`, license Apache).

## Arch / BlackArch

```bash
sudo pacman -S atear    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/NORMA-Inc/AtEar.git
cd AtEar
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `aircrack-ng`
  - `hostapd`
  - `nmap`
  - `python2`
  - `wireshark-cli`
- ⚠️ Python 2 only deps: python2-flask, python2-paramiko, python2-psycopg2, python2-pyodbc, python2-python-libnmap, python2-requests, python2-scapy, python2-smartencoding (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[wireless]] · [[atear]] · [[BlackArch Tools]]

