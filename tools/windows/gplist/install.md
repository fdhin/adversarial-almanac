---
tool: "gplist"
build-system: "windows"
upstream: "https://vidstromlabs.com/freetools/gplist/"
recipe: "blackarch"
source: "https://vidstromlabs.com/freetools/gplist/"
license: "custom:unknown"
blackarch-pkgver: "1.0"
---

# Installing gplist

> Lists information about the applied Group Policies.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/gplist) (version `1.0`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S gplist    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from https://vidstromlabs.com/freetools/gplist/
wine <tool>.exe
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[windows]] · [[gplist]] · [[BlackArch Tools]]

