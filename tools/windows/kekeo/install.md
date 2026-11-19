---
tool: "kekeo"
build-system: "windows"
upstream: "https://github.com/gentilkiwi/kekeo"
recipe: "blackarch"
source: "https://github.com/gentilkiwi/kekeo"
license: "custom:unknown"
blackarch-pkgver: "2.2.0_20211214"
---

# Installing kekeo

> A little toolbox to play with Microsoft Kerberos in C.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/kekeo) (version `2.2.0_20211214`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S kekeo    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from https://github.com/gentilkiwi/kekeo
wine <tool>.exe
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[windows]] · [[kekeo]] · [[BlackArch Tools]]

