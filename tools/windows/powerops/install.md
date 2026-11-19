---
tool: "powerops"
build-system: "windows"
upstream: "https://github.com/fdiskyou/PowerOPS"
recipe: "blackarch"
source: "https://github.com/fdiskyou/PowerOPS"
license: "custom:unknown"
blackarch-pkgver: "32.13fe55b"
---

# Installing powerops

> PowerShell Runspace Portable Post Exploitation Tool aimed at making Penetration Testing with PowerShell "easier".

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/powerops) (version `32.13fe55b`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S powerops    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from https://github.com/fdiskyou/PowerOPS
wine <tool>.exe
```

## Dependencies (from recipe)

- **Build tools**: `git`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[windows]] · [[powerops]] · [[BlackArch Tools]]

