---
tool: "klogger"
build-system: "windows"
upstream: "http://ntsecurity.nu/toolbox/klogger/"
recipe: "blackarch"
source: "http://ntsecurity.nu/toolbox/klogger/"
license: "custom"
blackarch-pkgver: "1.0"
---

# Installing klogger

> A keystroke logger for the NT-series of Windows.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/klogger) (version `1.0`, license custom).

## Arch / BlackArch

```bash
sudo pacman -S klogger    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from http://ntsecurity.nu/toolbox/klogger/
wine <tool>.exe
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[windows]] · [[klogger]] · [[BlackArch Tools]]

