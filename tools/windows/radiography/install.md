---
tool: "radiography"
build-system: "windows"
upstream: "http://www.security-projects.com/?RadioGraPhy"
recipe: "blackarch"
source: "http://www.security-projects.com/?RadioGraPhy"
license: "GPL"
blackarch-pkgver: "2"
---

# Installing radiography

> A forensic tool which grabs as much information as possible from a Windows system.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/radiography) (version `2`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S radiography    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from http://www.security-projects.com/?RadioGraPhy
wine <tool>.exe
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[windows]] · [[radiography]] · [[BlackArch Tools]]

