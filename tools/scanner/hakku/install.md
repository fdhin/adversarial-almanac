---
tool: "hakku"
build-system: "scripts"
upstream: "https://github.com/4shadoww/hakkuframework"
recipe: "blackarch"
source: "https://github.com/4shadoww/hakkuframework"
license: "BSD-3-Clause"
blackarch-pkgver: "1.0.r0.g409a11f"
---

# Installing hakku

> Simple framework that has been made for penetration testing tools.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/hakku) (version `1.0.r0.g409a11f`, license BSD-3-Clause).

## Arch / BlackArch

```bash
sudo pacman -S hakku    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/4shadoww/hakkuframework.git
cd hakkuframework
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `cryptography`, `dnspython`, `future`, `idna`, `lxml`, `netifaces`, `pyopenssl`, `python-whois`, `requests`, `scapy`, `six`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `aircrack-ng`
  - `bluez-utils`
  - `dsniff`
  - `ethtool`
  - `ettercap`
  - `python`
  - `sslstrip`
  - `tcpdump`
  - `xterm`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[hakku]] · [[BlackArch Tools]]

