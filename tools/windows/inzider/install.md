---
tool: "inzider"
build-system: "windows"
upstream: "https://vidstromlabs.com/freetools/inzider/"
recipe: "blackarch"
source: "https://vidstromlabs.com/freetools/inzider/"
license: "custom:unknown"
blackarch-pkgver: "1.2"
---

# Installing inzider

> This is a tool that lists processes in your Windows system and the ports each one listen on.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/inzider) (version `1.2`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S inzider    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from https://vidstromlabs.com/freetools/inzider/
wine <tool>.exe
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[windows]] · [[inzider]] · [[BlackArch Tools]]

