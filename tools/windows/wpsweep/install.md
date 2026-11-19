---
tool: "wpsweep"
build-system: "windows"
upstream: "https://vidstromlabs.com/freetools/wpsweep/"
recipe: "blackarch"
source: "https://vidstromlabs.com/downloads/wpsweep.exe"
license: "custom:unknown"
blackarch-pkgver: "1.0"
---

# Installing wpsweep

> A simple ping sweeper, that is, it pings a range of IP addresses and lists the ones that reply.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/wpsweep) (version `1.0`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S wpsweep    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from https://vidstromlabs.com/freetools/wpsweep/
wine <tool>.exe
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[windows]] · [[wpsweep]] · [[BlackArch Tools]]

