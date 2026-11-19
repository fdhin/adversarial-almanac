---
tool: "kerbcrack"
build-system: "windows"
upstream: "https://vidstromlabs.com/freetools/kerbcrack/"
recipe: "blackarch"
source: "https://vidstromlabs.com/freetools/kerbcrack/"
license: "custom:unknown"
blackarch-pkgver: "1.3d3"
---

# Installing kerbcrack

> Kerberos sniffer and cracker for Windows.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/kerbcrack) (version `1.3d3`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S kerbcrack    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from http://ntsecurity.nu/toolbox/kerbcrack/
wine <tool>.exe
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[windows]] · [[kerbcrack]] · [[BlackArch Tools]]

