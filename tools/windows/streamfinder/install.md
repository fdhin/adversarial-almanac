---
tool: "streamfinder"
build-system: "windows"
upstream: "https://www.gaijin.at/en/dlstreamfind.php"
recipe: "blackarch"
source: "https://www.gaijin.at/en/dlstreamfind.php"
license: "unknown"
blackarch-pkgver: "1.2"
---

# Installing streamfinder

> Searches for Alternate Data Streams (ADS).

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/streamfinder) (version `1.2`, license n/a).

## Arch / BlackArch

```bash
sudo pacman -S streamfinder    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from https://www.gaijin.at/en/dlstreamfind.php
wine <tool>.exe
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `curl`
  - `unrar`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[windows]] · [[streamfinder]] · [[BlackArch Tools]]

