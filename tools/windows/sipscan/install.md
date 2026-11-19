---
tool: "sipscan"
build-system: "windows"
upstream: "http://www.hackingvoip.com/sec_tools.html"
recipe: "blackarch"
source: "http://www.hackingvoip.com/tools/sipscan.msi"
license: "custom:unknown"
blackarch-pkgver: "0.1"
---

# Installing sipscan

> A sip scanner.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/sipscan) (version `0.1`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S sipscan    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from http://www.hackingvoip.com/sec_tools.html
wine <tool>.exe
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[windows]] · [[sipscan]] · [[BlackArch Tools]]

