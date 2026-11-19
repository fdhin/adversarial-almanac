---
tool: "wmd"
build-system: "scripts"
upstream: "https://github.com/ThomasTJdev/WMD"
recipe: "blackarch"
source: "https://github.com/ThomasTJdev/WMD"
license: "MIT"
blackarch-pkgver: "30.32e249a"
---

# Installing wmd

> Python framework for IT security tools.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/wmd) (version `30.32e249a`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S wmd    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/ThomasTJdev/WMD.git
cd WMD
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `beautifulsoup4`, `dateutil`, `flask`, `lxml`, `netifaces`, `paramiko`, `psutil`, `rarfile`, `requests`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`
  - `scapy`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[automation]] · [[wmd]] · [[BlackArch Tools]]

