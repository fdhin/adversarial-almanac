---
tool: "httprecon"
build-system: "windows"
upstream: "http://www.computec.ch/projekte/httprecon/?s=download"
recipe: "blackarch"
source: "http://www.computec.ch/projekte/httprecon/?s=download"
license: "custom"
blackarch-pkgver: "7.3"
---

# Installing httprecon

> Tool for web server fingerprinting, also known as http fingerprinting.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/httprecon) (version `7.3`, license custom).

## Arch / BlackArch

```bash
sudo pacman -S httprecon    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from http://www.computec.ch/projekte/httprecon/?s=download
wine <tool>.exe
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[windows]] · [[httprecon]] · [[BlackArch Tools]]

