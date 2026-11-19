---
tool: "sysinternals-suite"
build-system: "windows"
upstream: "http://sysinternals.com/"
recipe: "blackarch"
source: "http://download.sysinternals.com/files/SysinternalsSuite.zip"
license: "custom:unknown"
blackarch-pkgver: "7.6"
---

# Installing sysinternals-suite

> Sysinternals tools suite.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/sysinternals-suite) (version `7.6`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S sysinternals-suite    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from http://sysinternals.com/
wine <tool>.exe
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[windows]] · [[sysinternals-suite]] · [[BlackArch Tools]]

