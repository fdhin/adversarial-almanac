---
tool: "httprint-win32"
build-system: "windows"
upstream: "http://net-square.com/httprint"
recipe: "blackarch"
source: "http://net-square.com/httprint"
license: "custom"
blackarch-pkgver: "301"
---

# Installing httprint-win32

> A web server fingerprinting tool (Windows binaries).

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/httprint-win32) (version `301`, license custom).

## Arch / BlackArch

```bash
sudo pacman -S httprint-win32    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from http://net-square.com/httprint
wine <tool>.exe
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[windows]] · [[httprint-win32]] · [[BlackArch Tools]]

