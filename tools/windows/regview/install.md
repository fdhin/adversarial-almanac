---
tool: "regview"
build-system: "windows"
upstream: "https://www.gaijin.at/en/dlregview.php"
recipe: "blackarch"
source: "https://www.gaijin.at/downlo4d/regview.7z"
license: "unknown"
blackarch-pkgver: "1.3"
---

# Installing regview

> Open raw Windows NT 5 Registry files (Windows 2000 or higher).

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/regview) (version `1.3`, license n/a).

## Arch / BlackArch

```bash
sudo pacman -S regview    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from https://www.gaijin.at/en/dlregview.php
wine <tool>.exe
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `curl`
  - `p7zip`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[windows]] · [[regview]] · [[BlackArch Tools]]

