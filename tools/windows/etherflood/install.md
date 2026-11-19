---
tool: "etherflood"
build-system: "windows"
upstream: "http://ntsecurity.nu/toolbox/etherflood/"
recipe: "blackarch"
source: "http://ntsecurity.nu/toolbox/etherflood/"
license: "custom:unknown"
blackarch-pkgver: "1.1"
---

# Installing etherflood

> Floods a switched network with Ethernet frames with random hardware addresses.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/etherflood) (version `1.1`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S etherflood    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from http://ntsecurity.nu/toolbox/etherflood/
wine <tool>.exe
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[windows]] · [[etherflood]] · [[BlackArch Tools]]

