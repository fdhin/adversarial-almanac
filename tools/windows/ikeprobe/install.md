---
tool: "ikeprobe"
build-system: "windows"
upstream: "http://www.ernw.de/download/ikeprobe.zip"
recipe: "blackarch"
source: "http://www.ernw.de/download/ikeprobe.zip"
license: "custom"
blackarch-pkgver: "0.1"
---

# Installing ikeprobe

> Determine vulnerabilities in the PSK implementation of the VPN server.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/ikeprobe) (version `0.1`, license custom).

## Arch / BlackArch

```bash
sudo pacman -S ikeprobe    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from http://www.ernw.de/download/ikeprobe.zip
wine <tool>.exe
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[windows]] · [[ikeprobe]] · [[BlackArch Tools]]

