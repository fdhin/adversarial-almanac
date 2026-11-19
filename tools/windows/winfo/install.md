---
tool: "winfo"
build-system: "windows"
upstream: "https://vidstromlabs.com/freetools/winfo/"
recipe: "blackarch"
source: "https://vidstromlabs.com/freetools/winfo/"
license: "custom"
blackarch-pkgver: "2.0"
---

# Installing winfo

> Uses null sessions to remotely try to retrieve lists of and information about user accounts, workstation/interdomain/server trust accounts, shares (also hidden), sessions, logged in users, and password/lockout policy, from Windows NT/2000/XP.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/winfo) (version `2.0`, license custom).

## Arch / BlackArch

```bash
sudo pacman -S winfo    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from https://vidstromlabs.com/freetools/winfo/
wine <tool>.exe
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[windows]] · [[winfo]] · [[BlackArch Tools]]

