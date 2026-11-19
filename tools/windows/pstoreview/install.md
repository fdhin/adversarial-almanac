---
tool: "pstoreview"
build-system: "windows"
upstream: "https://vidstromlabs.com/freetools/pstoreview/"
recipe: "blackarch"
source: "https://vidstromlabs.com/freetools/pstoreview/"
license: "custom:unknown"
blackarch-pkgver: "1.0"
---

# Installing pstoreview

> Lists the contents of the Protected Storage.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/pstoreview) (version `1.0`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S pstoreview    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from https://vidstromlabs.com/freetools/pstoreview/
wine <tool>.exe
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[windows]] · [[pstoreview]] · [[BlackArch Tools]]

