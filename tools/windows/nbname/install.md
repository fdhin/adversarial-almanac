---
tool: "nbname"
build-system: "windows"
upstream: "http://www.cultdeadcow.com/tools/bo.html"
recipe: "blackarch"
source: "http://www.cultdeadcow.com/tools/bo.html"
license: "custom:unknown"
blackarch-pkgver: "1.0"
---

# Installing nbname

> Decodes and displays all NetBIOS name packets it receives on UDP port 137 and more!

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/nbname) (version `1.0`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S nbname    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from http://www.cultdeadcow.com/tools/bo.html
wine <tool>.exe
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[windows]] · [[nbname]] · [[BlackArch Tools]]

