---
tool: "justdecompile"
build-system: "windows"
upstream: "https://github.com/telerik/JustDecompileEngine/tags"
recipe: "blackarch"
source: "https://github.com/telerik/JustDecompileEngine/tags"
license: "APACHE"
blackarch-pkgver: "22018"
---

# Installing justdecompile

> The decompilation engine of JustDecompile.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/justdecompile) (version `22018`, license APACHE).

## Arch / BlackArch

```bash
sudo pacman -S justdecompile    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from https://github.com/telerik/JustDecompileEngine/releases
wine <tool>.exe
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[windows]] · [[justdecompile]] · [[BlackArch Tools]]

