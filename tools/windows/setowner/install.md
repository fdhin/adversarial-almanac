---
tool: "setowner"
build-system: "windows"
upstream: "https://vidstromlabs.com/freetools/setowner/"
recipe: "blackarch"
source: "https://vidstromlabs.com/freetools/setowner/"
license: "custom:unknown"
blackarch-pkgver: "1.1"
---

# Installing setowner

> Allows you to set file ownership to any account, as long as you have the "Restore files and directories" user right.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/setowner) (version `1.1`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S setowner    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from http://ntsecurity.nu/toolbox/setowner/
wine <tool>.exe
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[windows]] · [[setowner]] · [[BlackArch Tools]]

