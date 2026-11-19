---
tool: "wups"
build-system: "windows"
upstream: "http://ntsecurity.nu/toolbox/wups/"
recipe: "blackarch"
source: "http://ntsecurity.nu/toolbox/wups/"
license: "custom:unknown"
blackarch-pkgver: "1.4"
---

# Installing wups

> An UDP port scanner for Windows.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/wups) (version `1.4`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S wups    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from http://ntsecurity.nu/toolbox/wups/
wine <tool>.exe
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[windows]] · [[wups]] · [[BlackArch Tools]]

