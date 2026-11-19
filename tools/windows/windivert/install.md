---
tool: "windivert"
build-system: "windows"
upstream: "https://github.com/basil00/Divert"
recipe: "blackarch"
source: "https://github.com/basil00/Divert"
license: "LGPL3"
blackarch-pkgver: "2.2.0"
---

# Installing windivert

> A user-mode packet capture-and-divert package for Windows.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/windivert) (version `2.2.0`, license LGPL3).

## Arch / BlackArch

```bash
sudo pacman -S windivert    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from https://github.com/basil00/Divert
wine <tool>.exe
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[windows]] · [[windivert]] · [[BlackArch Tools]]

