---
tool: "periscope"
build-system: "windows"
upstream: "https://vidstromlabs.com/freetools/periscope/"
recipe: "blackarch"
aur-package: "periscope"
source: "https://vidstromlabs.com/freetools/periscope/"
license: "custom:unknown"
blackarch-pkgver: "3.2"
---

# Installing periscope

> A PE file inspection tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/periscope) (version `3.2`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S periscope    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from https://vidstromlabs.com/freetools/periscope/
wine <tool>.exe
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[windows]] · [[periscope]] · [[BlackArch Tools]]

