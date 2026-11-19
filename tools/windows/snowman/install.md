---
tool: "snowman"
build-system: "windows"
upstream: "http://derevenets.com/"
recipe: "blackarch"
aur-package: "snowman"
source: "http://derevenets.com/"
license: "GPL2"
blackarch-pkgver: "0.1.3"
---

# Installing snowman

> A native code to C/C++ decompiler, see the examples of generated code.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/snowman) (version `0.1.3`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S snowman    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from http://derevenets.com/
wine <tool>.exe
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[windows]] · [[snowman]] · [[BlackArch Tools]]

