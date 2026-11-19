---
tool: "3proxy-win32"
build-system: "windows"
upstream: "http://3proxy.ru/"
recipe: "blackarch"
source: "http://3proxy.ru/"
license: "Apache-2.0, GPL-2.0-or-later"
blackarch-pkgver: "0.9.6.1"
---

# Installing 3proxy-win32

> Tiny free proxy server.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/3proxy-win32) (version `0.9.6.1`, license Apache-2.0, GPL-2.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S 3proxy-win32    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from http://3proxy.ru/
wine <tool>.exe
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[windows]] · [[3proxy-win32]] · [[BlackArch Tools]]

