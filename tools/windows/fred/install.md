---
tool: "fred"
build-system: "windows"
upstream: "https://www.pinguin.lu/fred"
recipe: "blackarch"
aur-package: "fred"
source: "https://www.pinguin.lu/fred"
license: "GPL3"
blackarch-pkgver: "0.2.0"
---

# Installing fred

> Cross-platform M$ registry hive editor.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/fred) (version `0.2.0`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S fred    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from https://www.pinguin.lu/fred
wine <tool>.exe
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `hivex`
  - `qt4`
  - `qtwebkit`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[windows]] · [[fred]] · [[BlackArch Tools]]

