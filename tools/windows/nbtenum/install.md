---
tool: "nbtenum"
build-system: "windows"
upstream: "http://reedarvin.thearvins.com/"
recipe: "blackarch"
source: "https://dl.packetstormsecurity.net/Win/NBTEnum33.zip"
license: "custom"
blackarch-pkgver: "3.3"
---

# Installing nbtenum

> A utility for Windows that can be used to enumerate NetBIOS information from one host or a range of hosts.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/nbtenum) (version `3.3`, license custom).

## Arch / BlackArch

```bash
sudo pacman -S nbtenum    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from http://reedarvin.thearvins.com/
wine <tool>.exe
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[windows]] · [[nbtenum]] · [[BlackArch Tools]]

