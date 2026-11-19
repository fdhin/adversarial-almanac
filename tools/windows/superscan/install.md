---
tool: "superscan"
build-system: "windows"
upstream: "http://www.foundstone.com/us/resources/proddesc/superscan.htm"
recipe: "blackarch"
source: "http://www.foundstone.com/us/resources/proddesc/superscan.htm"
license: "custom:unknown"
blackarch-pkgver: "4.1"
---

# Installing superscan

> Powerful TCP port scanner, pinger, resolver.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/superscan) (version `4.1`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S superscan    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from http://www.foundstone.com/us/resources/proddesc/superscan.htm
wine <tool>.exe
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[windows]] · [[superscan]] · [[BlackArch Tools]]

