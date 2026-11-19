---
tool: "windowsspyblocker"
build-system: "windows"
upstream: "https://github.com/crazy-max/WindowsSpyBlocker/"
recipe: "blackarch"
source: "https://github.com/crazy-max/WindowsSpyBlocker/"
license: "MIT"
blackarch-pkgver: "4.38.0"
---

# Installing windowsspyblocker

> Block spying and tracking on Windows.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/windowsspyblocker) (version `4.38.0`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S windowsspyblocker    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from https://github.com/crazy-max/WindowsSpyBlocker/
wine <tool>.exe
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[windows]] · [[windowsspyblocker]] · [[BlackArch Tools]]

