---
tool: "lolbas"
build-system: "windows"
upstream: "https://github.com/api0cradle/LOLBAS"
recipe: "blackarch"
source: "https://github.com/api0cradle/LOLBAS"
license: "unknown"
blackarch-pkgver: "192.d148d27"
---

# Installing lolbas

> Living Off The Land Binaries And Scripts - (LOLBins and LOLScripts).

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/lolbas) (version `192.d148d27`, license n/a).

## Arch / BlackArch

```bash
sudo pacman -S lolbas    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from https://github.com/api0cradle/LOLBAS
wine <tool>.exe
```

## Dependencies (from recipe)

- **Build tools**: `git`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[windows]] · [[lolbas]] · [[BlackArch Tools]]

