---
tool: "mrkaplan"
build-system: "windows"
upstream: "https://github.com/Idov31/MrKaplan"
recipe: "blackarch"
source: "https://github.com/Idov31/MrKaplan"
license: "BSD"
blackarch-pkgver: "1.1.1"
---

# Installing mrkaplan

> Help red teamers to stay hidden by clearing evidence of execution.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/mrkaplan) (version `1.1.1`, license BSD).

## Arch / BlackArch

```bash
sudo pacman -S mrkaplan    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from https://github.com/Idov31/MrKaplan
wine <tool>.exe
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[windows]] · [[mrkaplan]] · [[BlackArch Tools]]

