---
tool: "rasenum"
build-system: "windows"
upstream: "http://www.cultdeadcow.com/tools/rasenum.html"
recipe: "blackarch"
source: "http://www.cultdeadcow.com/tools/rasenum.html"
license: "custom:unknown"
blackarch-pkgver: "1.0"
---

# Installing rasenum

> A small program which lists the information for all of the entries in any phonebook file (.pbk).

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/rasenum) (version `1.0`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S rasenum    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from http://www.cultdeadcow.com/tools/rasenum.html
wine <tool>.exe
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[windows]] · [[rasenum]] · [[BlackArch Tools]]

