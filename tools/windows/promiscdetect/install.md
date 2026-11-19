---
tool: "promiscdetect"
build-system: "windows"
upstream: "https://vidstromlabs.com/freetools/promiscdetect"
recipe: "blackarch"
source: "https://vidstromlabs.com/freetools/promiscdetect"
license: "custom:unknown"
blackarch-pkgver: "1.0"
---

# Installing promiscdetect

> Checks if your network adapter(s) is running in promiscuous mode, which may be a sign that you have a sniffer running on your computer.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/promiscdetect) (version `1.0`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S promiscdetect    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from https://vidstromlabs.com/freetools/promiscdetect
wine <tool>.exe
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[windows]] · [[promiscdetect]] · [[BlackArch Tools]]

