---
tool: "hikpwn"
build-system: "scripts"
upstream: "https://github.com/4n4nk3/HikPwn"
recipe: "blackarch"
source: "https://github.com/4n4nk3/HikPwn"
license: "GPL3"
blackarch-pkgver: "8.5a7d69c"
---

# Installing hikpwn

> A simple scanner for Hikvision devices with basic vulnerability scanning capabilities written in Python 3.8.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/hikpwn) (version `8.5a7d69c`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S hikpwn    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/4n4nk3/HikPwn.git
cd HikPwn
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `lxml`, `requests`, `scapy`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[hikpwn]] · [[BlackArch Tools]]

