---
tool: "regreport"
build-system: "windows"
upstream: "https://www.gaijin.at/dlregreport.php"
recipe: "blackarch"
source: "https://www.gaijin.at/dlregreport.php"
license: "unknown"
blackarch-pkgver: "1.6"
---

# Installing regreport

> Windows registry forensic analysis tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/regreport) (version `1.6`, license n/a).

## Arch / BlackArch

```bash
sudo pacman -S regreport    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from https://www.gaijin.at/dlregreport.php
wine <tool>.exe
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `curl`
  - `p7zip`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[windows]] · [[regreport]] · [[BlackArch Tools]]

