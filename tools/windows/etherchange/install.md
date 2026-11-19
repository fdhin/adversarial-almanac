---
tool: "etherchange"
build-system: "windows"
upstream: "https://vidstromlabs.com/freetools/etherchange/"
recipe: "blackarch"
source: "https://vidstromlabs.com/freetools/etherchange/"
license: "custom:unknown"
blackarch-pkgver: "1.1"
---

# Installing etherchange

> Can change the Ethernet address of the network adapters in Windows.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/etherchange) (version `1.1`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S etherchange    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from https://vidstromlabs.com/freetools/etherchange/
wine <tool>.exe
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[windows]] · [[etherchange]] · [[BlackArch Tools]]

