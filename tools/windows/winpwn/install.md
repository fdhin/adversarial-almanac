---
tool: "winpwn"
build-system: "windows"
upstream: "https://github.com/S3cur3Th1sSh1t/WinPwn"
recipe: "blackarch"
source: "https://github.com/S3cur3Th1sSh1t/WinPwn"
license: "BSD"
blackarch-pkgver: "411.cba1769"
---

# Installing winpwn

> Automation for internal Windows Penetrationtest / AD-Security.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/winpwn) (version `411.cba1769`, license BSD).

## Arch / BlackArch

```bash
sudo pacman -S winpwn    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from https://github.com/S3cur3Th1sSh1t/WinPwn
wine <tool>.exe
```

## Dependencies (from recipe)

- **Build tools**: `git`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[windows]] · [[winpwn]] · [[BlackArch Tools]]

