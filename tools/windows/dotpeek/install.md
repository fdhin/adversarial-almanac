---
tool: "dotpeek"
build-system: "windows"
upstream: "https://www.jetbrains.com/decompiler/"
recipe: "blackarch"
aur-package: "dotpeek"
source: "https://www.jetbrains.com/decompiler/"
license: "custom:unknown"
blackarch-pkgver: "2021.3.3"
---

# Installing dotpeek

> Free .NET Decompiler and Assembly Browser.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/dotpeek) (version `2021.3.3`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S dotpeek    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from https://www.jetbrains.com/decompiler/
wine <tool>.exe
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[windows]] · [[dotpeek]] · [[BlackArch Tools]]

