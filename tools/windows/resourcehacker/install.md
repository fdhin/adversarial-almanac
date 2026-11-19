---
tool: "resourcehacker"
build-system: "windows"
upstream: "http://www.angusj.com/resourcehacker/"
recipe: "blackarch"
source: "http://www.angusj.com/resourcehacker/"
license: "unknown"
blackarch-pkgver: "5.1.8"
---

# Installing resourcehacker

> Resource compiler and decompiler for Windows® applications.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/resourcehacker) (version `5.1.8`, license n/a).

## Arch / BlackArch

```bash
sudo pacman -S resourcehacker    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from http://www.angusj.com/resourcehacker/
wine <tool>.exe
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[windows]] · [[resourcehacker]] · [[BlackArch Tools]]

