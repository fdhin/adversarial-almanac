---
tool: "x64dbg"
build-system: "windows"
upstream: "https://github.com/x64dbg/x64dbg/releases"
recipe: "blackarch"
source: "https://github.com/x64dbg/x64dbg/releases"
license: "GPL3"
blackarch-pkgver: "2025.07.04"
---

# Installing x64dbg

> An open-source x64/x32 debugger for windows.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/x64dbg) (version `2025.07.04`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S x64dbg    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from https://github.com/x64dbg/x64dbg/releases
wine <tool>.exe
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[windows]] · [[x64dbg]] · [[BlackArch Tools]]

