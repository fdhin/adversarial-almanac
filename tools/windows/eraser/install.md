---
tool: "eraser"
build-system: "windows"
upstream: "https://eraser.heidi.ie/download/"
recipe: "blackarch"
source: "https://eraser.heidi.ie/download/"
license: "custom:unknown"
blackarch-pkgver: "6.2.0.2992"
---

# Installing eraser

> Windows tool which allows you to completely remove sensitive data from your hard drive by overwriting it several times with carefully selected patterns.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/eraser) (version `6.2.0.2992`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S eraser    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from https://eraser.heidi.ie/download/
wine <tool>.exe
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[windows]] · [[eraser]] · [[BlackArch Tools]]

