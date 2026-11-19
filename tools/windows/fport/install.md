---
tool: "fport"
build-system: "windows"
upstream: "http://www.foundstone.com/us/resources/proddesc/fport.htm"
recipe: "blackarch"
source: "https://dl.packetstormsecurity.net/Win2k/fport.zip"
license: "custom:unknown"
blackarch-pkgver: "2.0"
---

# Installing fport

> Identify unknown open ports and their associated applications.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/fport) (version `2.0`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S fport    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from http://www.foundstone.com/us/resources/proddesc/fport.htm
wine <tool>.exe
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[windows]] · [[fport]] · [[BlackArch Tools]]

