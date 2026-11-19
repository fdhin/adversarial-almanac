---
tool: "winhex"
build-system: "windows"
upstream: "https://www.x-ways.net/winhex/"
recipe: "blackarch"
source: "https://www.x-ways.net/winhex/"
license: "unknown"
blackarch-pkgver: "20.4"
---

# Installing winhex

> Hex Editor and Disk Editor.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/winhex) (version `20.4`, license n/a).

## Arch / BlackArch

```bash
sudo pacman -S winhex    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from https://www.x-ways.net/winhex/
wine <tool>.exe
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[windows]] · [[winhex]] · [[BlackArch Tools]]

