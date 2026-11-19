---
tool: "adape-script"
build-system: "windows"
upstream: "https://github.com/hausec/ADAPE-Script"
recipe: "blackarch"
source: "https://github.com/hausec/ADAPE-Script"
license: "custom:unknown"
blackarch-pkgver: "43.4d0b9ff"
---

# Installing adape-script

> Active Directory Assessment and Privilege Escalation Script.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/adape-script) (version `43.4d0b9ff`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S adape-script    # needs the [blackarch] repo enabled
```

## Any Linux distro

⚠️ **Windows payload.** On Linux, run under [Wine](https://www.winehq.org):

```bash
sudo apt install wine64   # or dnf install wine
# download the tool from https://github.com/hausec/ADAPE-Script
wine <tool>.exe
```

## Dependencies (from recipe)

- **Build tools**: `git`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/windows`

Backlinks: [[windows]] · [[adape-script]] · [[BlackArch Tools]]

