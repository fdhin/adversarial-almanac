---
tool: "wifichannelmonitor"
build-system: "windows"
upstream: "https://www.nirsoft.net/utils/wifi_channel_monitor.html"
recipe: "blackarch"
source: "http://www.nirsoft.net/utils/wifichannelmonitor.zip"
license: "custom:unknown"
blackarch-pkgver: "1.70"
---

# Installing wifichannelmonitor

> A utility for Windows that captures wifi traffic on the channel you choose, using Microsoft Network Monitor capture driver.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/wifichannelmonitor) (version `1.70`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S wifichannelmonitor    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from https://www.nirsoft.net/utils/wifi_channel_monitor.html
wine <tool>.exe
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `unzip`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[windows]] · [[wifichannelmonitor]] · [[BlackArch Tools]]

